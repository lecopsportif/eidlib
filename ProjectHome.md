The e-ID library is a user-friendly Java library to perform operations on the Belgian electronic identity card.

Since the introduction of the e-ID (electronic identity card) in Belgium there have been a growing number of applications using it. Except for being just a proof of identify, you can perform a vast number of administrative issues from your computer, sign electronic documents and e-mails and have safe chat session for children. A complete description of many of the applications available can be found at http://map.eid.belgium.be. With a growing number of applications, there is an increasing need for support for the e-ID in multiple languages, especially Java, which has grown to become one of the most popular programming languages.

For a long time, the problem with Java was that due to it’s platform independence, driver functionality could not be done in native Java. The only way to circumvent this was to apply JNI (Java Native Interface) which allows native calls to C (or C++, assembly, …). In the original middleware as issued by Fedict a JNI layer is present which allows Java programmers to perform operations on the e-ID card. With the introduction of Java 6 came the javax.smartcardio library (the API can be found [here](http://java.sun.com/javase/6/docs/jre/api/security/smartcardio/spec/index.html?javax/smartcardio/package-summary.html)). With this library, one can send APDU messages to smart card readers.

The e-ID library provides a very user-friendly and well documented Java library that can be used without any need for an installation, the only thing that needs to be done is to import the JAR file.

The e-ID library was a Bachelor thesis assignment by CoMP (http://www.comp.ua.ac.be) to gain the degree of Bachelor at the university of Antwerp. The supervisors for this assignment were Dr. Gunther Stuer, employee at Fedict, and Prof. Dr. Jan Broeckhove, professor at the university of Antwerp and head of CoMP.

**The e-ID library can be downloaded from the Downloads page, along with the BEIDGui. The sample applications can downloaded/browsed from the SVN at the Source page.**

**The e-ID library can now be found on github: https://github.com/KeejOow/eidlib**
