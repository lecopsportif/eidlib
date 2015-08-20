### Preliminary ###

Java 6 or higher is required for the library to work. This implies that the library won't work on mac OS X 32bit since Java 6 has still not been released for this platform.
The driver for the inserted smart-card reader must also be installed. For Windows computers this is usually done automatically.

### Download the library ###

On the downloads page can you find the library (jEidlib.jar), along with some sample applications and a GUI that provides a graphical representation of the e-ID card.

### Import into project ###

To use the library, the only thing you need to do is to import the library into your project. For a command-line project, this would mean to add jEidlib.jar to the build path. In an IDE such as for example Eclipse, you need click on 'Project > Properties > Java Build Path' and add jEidlib.jar. These steps do not differ from using any other java library wrapped in a JAR file.