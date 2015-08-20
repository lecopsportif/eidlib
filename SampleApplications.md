All of our sample applications can be found on the ‘Downloads’ page.

### BEIDGUI ###
The first and most extensive sample application is a Graphical User Interface demonstrating the e-ID library. Although this application is not really a sample application to learn using the library since the code is probably harder to read than that of the library itself. The application is therefore more like a useful application to graphically represent a Belgium e-ID card. Functionality of this GUI includes the saving of the information on the e-ID card to an XML-file and reading it again afterwards. There is also a print function available that is capable of printing the information on the e-ID card.

Since we are already talking about the saving of information to an XML-file. This is a feature which I would like to include in the e-ID library once, I’m not sure yet whether it belongs there.

This BEIDGUI however is currently only served as a sample application and not as a robust and fully fletched application. There is not yet support for multiple languages, no configuration settings can be applied (certificates are always verified instead, this choice should however be left to the user), there could be some uncaught stack traces when performing exceptional operations, etc.

### Age check ###
The Age check sample application allows a user to verify it’s age to exceed a certain value. For example when going to a Movies complex, there is a need for verification whether a certain person is old enough to see an adult movie. Other means could be for buying alcohol, cigarettes, violent video games, etc … .

First the given argument that should contain the age is parsed into a valid integral number. Then the e-ID interface is constructed. Then the card polling system is defined by defining the card listener. When a card is inserted, the birth date is retrieved, then the birth date that the person should be born before is calculated. Finally both values and compared and the output is shown.

```
package be.belgium.eid.samples;

import java.util.Date;
import java.util.Scanner;

import be.belgium.eid.eidlib.BeID;
import be.belgium.eid.event.CardAdapter;
import be.belgium.eid.exceptions.EIDException;

public class AgeCheck {
	
	public static void main(String[] args) {
		// The first and only argument that the program should receive is the age to verify
		if (args.length != 1) {
			System.err.println("AgeChecker -- Invalid number of arguments.");
		} else {
			try {
				final Long ageToCheck = Long.parseLong(args[0]);	// Age to verify
				if (ageToCheck < 0) {
					System.err.println("AgeChecker -- Given argument should be a positive value.");
				} else {	
					// Load the eID 
					final BeID eID = new BeID(false);	// We don't allow test cards to verify their age
					eID.enableCardListener(new CardAdapter() {
						public void cardInserted() {
							// We now check the age
							try {
								// Fetch birth date inserted card
								Date birthdate = eID.getIDData().getBirthDate();

								// Calculate what the upperbound of the birthdate is
								// (We ignore Leap Years)
								long timeBeforeNow = ageToCheck * 365 /* DAYS A YEAR */ 
										* 24 /* HOURS A DAY */ * 60 /* MINUTES PER HOUR */
										* 60 /* SEC/MINUTE */ * 1000;
								Date upperbound = new Date();
								upperbound.setTime(upperbound.getTime() - timeBeforeNow);
								
								// Check age
								if (birthdate.after(upperbound)) {
									System.out.println("AgeChecker -- Too young.");
								} else {
									System.out.println("AgeChecker -- OK.");
								}
							} catch (EIDException e) {
								e.printStackTrace(); 
								System.err.println("AgeChecker eIDException: -- " + e.getMessage());
							} catch (Exception e) {
								System.err.println("AgeChecker exception: -- " + e.getMessage());
							}
							
						}
					});
					
					// Keep on checking ages until QUIT is typed or process is ended
					System.out.println("AgeChecker -- type QUIT to end program.");
					String input = "";
					while (!input.equalsIgnoreCase("QUIT")) {
						input = new Scanner(System.in).next();
					}
				}
			} catch (NumberFormatException e) {
				System.err.println("AgeChecker -- Given argument should be an integral value.");
			}
		}
	}
}
```

### Information retrieval ###
The information retrieval sample application fetches and prints all the information of the inserted e-ID card. The picture is saved to a file. The e-ID card ought to be inserted before running this program. The program shown beneath is rather self-explanatory.

```
package be.belgium.eid.samples;

import be.belgium.eid.eidlib.BeID;
import be.belgium.eid.exceptions.EIDException;

public class InformationRetrieval {

	public static void main(String[] args) {
		// Load the eID
		try {
			BeID eID = new BeID(true); // We allow information to be fetched from test cards
			
			// We fetch the information
			System.out.println("InformationRetrieval -- ID information:");
			System.out.println(eID.getIDData().toString());
			System.out.println("InformationRetrieval -- Address information:");
			System.out.println(eID.getIDAddress().toString());
			System.out.println("InformationRetrieval -- Photo is saved to file:");
			eID.getIDPhoto().writeToFile(eID.getIDData().getName());
		} catch (EIDException e) {
			System.err.println("InformationRetrieval eIDException -- " + e.getMessage());
		} catch (Exception e) {
			System.err.println("InformationRetrieval exception -- " + e.getMessage());
		}
	}
}

```

### Security challenge ###
In our daily computer activities we need passwords all the time. Although it is not advised to use the same password over and over, most of the users do this anyway. Another bad habit is to have a file on your PC that contains all the passwords you use. A way to circumvent these disadvantages is to use challenges. With the e-ID card you can generate challenges, which are random sequences of bytes, based on the information on your smart card (which is more unique than most random number generators). Although these challenges are random, the e-ID can generate seemly random challenge responses that are fixes for each challenge. For each generated challenge, the response is always the same for that challenge. So what our plan is, is to generate a few challenges first (one per website for example) and to write these down or save these to a file or anything. When having generated a challenge, the challenge and challenge response is printed in a hexadecimal format so that we can read it without having non-printable characters cluttering the output. Then we enter the challenge response as password to our website (after small modifications if this is necessary). When we need to enter the password to the website later on, we enter the challenge as an argument to a program and generate the challenge response, now the password is again visible. The main idea for this method     is of course that you don't give your e-ID card to other people and to keep it safe with you.
package be.belgium.eid.samples;

```
package be.belgium.eid.samples;

import be.belgium.eid.eidcommon.ByteConverter;
import be.belgium.eid.eidlib.BeID;
import be.belgium.eid.exceptions.EIDException;
import be.belgium.eid.security.CertificateChain;
import be.belgium.eid.security.RNCertificate;

public class SecurityChallenge {

	public static void main(String[] args) {
		// Load the eID
		try {
			BeID eID = new BeID(false); // We don't allow test cards since they
										// could generate equal responses for 
										// different people and thus defeat security
			
			// First we need to pay attention to forgery so we check the validity
			CertificateChain certChain = eID.getCertificateChain();
			RNCertificate rnCert = eID.getNationalRegisterCertificate();
			System.out.println("Checking certificate validity... ");
			if (eID.verifyOCSP(certChain) && eID.verifyCRL(certChain, rnCert)) {
				System.out.println("SecurityChallenge -- certificates validated OK.");
				
				// Parse the arguments and process
				if (args.length == 0) {
					// Generate challenge and response when no arguments are given
					byte[] challenge = eID.getChallenge();
					byte[] challengeResponse = eID.getChallengeResponse(challenge);
	
					// Print challenge and response
					System.out.println("Challenge: "
							+ ByteConverter.hexify(challenge));
					System.out.println("Challenge response: "
							+ ByteConverter.hexify(challengeResponse));
				} else {
					// For each given challenge, generate response and print
					// information
					for (int i = 0; i < args.length; i++) {
						byte[] challenge = ByteConverter.unhexify(args[i]);
						byte[] challengeResponse = eID
								.getChallengeResponse(challenge);
	
						System.out.println("For challenge " + args[i]
								+ ", the response is: "
								+ ByteConverter.hexify(challengeResponse));
	
					}
				}
			} else {
				System.out.println("SecurityChallenge -- certificates were invalid or revoked.");
			}
		} catch (EIDException e) {
			System.err.println("SecurityChallenge eIDException -- "
					+ e.getMessage());
		} catch (Exception e) {
			System.err.println("SecurityChallenge exception -- "
					+ e.getMessage());
		}
	}

}
```

### Sign And Verify ###
The sign and verify sample application shows how easy it is to sign data. This data can be text as well as binary data, as long as it can be represented in a byte array. Since different certificates on the e-ID card can be used to sign and verify data, the choice can be made by the user. It is however important to note that the data must be verified by the public key of the same certificate. After the signature has been made, the signature is verified against it's data.

```
package be.belgium.eid.samples;

import be.belgium.eid.eidcommon.ByteConverter;
import be.belgium.eid.eidlib.BeID;
import be.belgium.eid.eidlib.BeID.SignatureType;
import be.belgium.eid.exceptions.EIDException;

public class SignAndVerify {

	/**
	 * Main program that signs and verifies the data
	 * 
	 * @param args
	 *            1 argument needs to be given: the PIN code. This is needed to
	 *            generate the signature
	 */
	public static void main(String[] args) {
		// The first and only argument that the program should receive is the
		// age to verify
		if (args.length != 1) {
			System.err.println("SignAndVerify -- Invalid number of arguments.");
		} else {
			final String textToSign = "And still no Java 6 for Mac OS X...";

			// Load the eID
			try {
				final BeID eID = new BeID(false); // We don't allow test cards
				byte[] signature = eID.generateSignature(textToSign.getBytes(),
						args[0], SignatureType.AUTHENTICATIONSIG);
				System.out.println("Signature: "
						+ ByteConverter.hexify(signature));
				System.out.println("Verification succeeded: "
						+ eID.verifySignature(textToSign.getBytes(), signature,
								SignatureType.AUTHENTICATIONSIG));
			} catch (EIDException e) {
				System.err.println("SignAndVerify -- EIDException: "
						+ e.getMessage());
			}
		}
	}
}

```