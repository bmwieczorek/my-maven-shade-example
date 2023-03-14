mvn dependency:tree -Dverbose
[INFO] --- dependency:2.8:tree (default-cli) @ my-app-client ---
[INFO] com.bawi:my-app-client:jar:0.1-SNAPSHOT
[INFO] +- com.bawi.base:my-base:jar:0.2-SNAPSHOT:compile
[INFO] \- com.bawi:my-app:jar:0.1-SNAPSHOT:compile
[INFO]    \- (com.bawi.base:my-base:jar:0.1-SNAPSHOT:compile - omitted for conflict with 0.2-SNAPSHOT)

#######################################
# without shading, no uberjar - error #
#######################################

mvn clean install
java -cp my-app-client/target/my-app-client-0.1-SNAPSHOT.jar:my-app/target/my-app-0.1-SNAPSHOT.jar:my-new-base/target/my-base-0.2-SNAPSHOT.jar:my-old-base/target/my-base-0.1-SNAPSHOT.jar com.bawi.AppClient

Hello app!
Exception in thread "main" java.lang.NoSuchMethodError: 'void com.bawi.base.Util.print(java.lang.String)'
        at com.bawi.App.print(App.java:8)
        at com.bawi.AppClient.main(AppClient.java:5)

# only com/bawi/App.class
unzip -l my-app/target/my-app-0.1-SNAPSHOT.jar
Archive:  my-app/target/my-app-0.1-SNAPSHOT.jar
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  03-14-2023 10:06   META-INF/
       81  03-14-2023 10:06   META-INF/MANIFEST.MF
        0  03-14-2023 10:06   com/
        0  03-14-2023 10:06   com/bawi/
        0  03-14-2023 10:06   META-INF/maven/
        0  03-14-2023 10:06   META-INF/maven/com.bawi/
        0  03-14-2023 10:06   META-INF/maven/com.bawi/my-app/
      513  03-14-2023 10:06   com/bawi/App.class
     2679  03-14-2023 08:52   META-INF/maven/com.bawi/my-app/pom.xml
       56  03-14-2023 10:06   META-INF/maven/com.bawi/my-app/pom.properties
---------                     -------
     3329                     10 files


# unzip my-app and inspect jar decompiled classes - App uses com/bawi/base/Util.print:(Ljava/lang/String;)V
unzip my-app/target/my-app-0.1-SNAPSHOT.jar -d my-app/target/
javap -c my-app/target/com/bawi/App.class
Compiled from "App.java"
public class com.bawi.App {
  public com.bawi.App();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void print();
    Code:
       0: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #13                 // String Hello app!
       5: invokevirtual #15                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: ldc           #21                 // String base
      10: invokestatic  #23                 // Method com/bawi/base/Util.print:(Ljava/lang/String;)V
      13: return
}

#######################################
# with shading in uberjar  - no error #
#######################################

mvn clean install -Pshade
java -cp my-app-client/target/my-app-client-0.1-SNAPSHOT.jar:my-app/target/my-app-0.1-SNAPSHOT.jar:my-new-base/target/my-base-0.2-SNAPSHOT.jar com.bawi.AppClient

Hello app!
Hello base
Hello base version 2


# com/bawi/App.class with shaded/com/bawi/base/Util.class in uberjar
unzip -l my-app/target/my-app-0.1-SNAPSHOT.jar

Archive:  my-app/target/my-app-0.1-SNAPSHOT.jar
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  03-14-2023 08:53   com/
        0  03-14-2023 08:53   com/bawi/
      520  03-14-2023 08:53   com/bawi/App.class
        0  03-14-2023 08:52   META-INF/
        0  03-14-2023 08:52   META-INF/maven/
        0  03-14-2023 08:52   META-INF/maven/com.bawi/
        0  03-14-2023 08:52   META-INF/maven/com.bawi/my-app/
     2679  03-14-2023 08:52   META-INF/maven/com.bawi/my-app/pom.xml
       56  03-14-2023 08:53   META-INF/maven/com.bawi/my-app/pom.properties
        0  03-14-2023 08:53   shaded/
        0  03-14-2023 08:53   shaded/com/
        0  03-14-2023 08:53   shaded/com/bawi/
        0  03-14-2023 08:53   shaded/com/bawi/base/
      979  03-14-2023 08:53   shaded/com/bawi/base/Util.class
        0  03-14-2023 08:21   META-INF/maven/com.bawi.base/
        0  03-14-2023 08:21   META-INF/maven/com.bawi.base/my-base/
      650  03-14-2023 08:21   META-INF/maven/com.bawi.base/my-base/pom.xml
       62  03-14-2023 08:53   META-INF/maven/com.bawi.base/my-base/pom.properties
---------                     -------
     4946                     18 files


# unzip my-app and inspect jar decompiled classes - App uses shaded/com/bawi/base/Util.print:(Ljava/lang/String;)V
unzip my-app/target/my-app-0.1-SNAPSHOT.jar -d my-app/target/
javap -c my-app/target/com/bawi/App.class

Compiled from "App.java"
public class com.bawi.App {
  public com.bawi.App();
    Code:
       0: aload_0
       1: invokespecial #9                  // Method java/lang/Object."<init>":()V
       4: return

  public static void print();
    Code:
       0: getstatic     #18                 // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #20                 // String Hello app!
       5: invokevirtual #26                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: ldc           #28                 // String base
      10: invokestatic  #32                 // Method shaded/com/bawi/base/Util.print:(Ljava/lang/String;)V
      13: return
}


javap -c my-app/target/shaded/com/bawi/base/Util.class
Compiled from "Util.java"
public class shaded.com.bawi.base.Util {
  public shaded.com.bawi.base.Util();
    Code:
       0: aload_0
       1: invokespecial #14                 // Method java/lang/Object."<init>":()V
       4: return

  public static void print(java.lang.String);
    Code:
       0: getstatic     #24                 // Field java/lang/System.out:Ljava/io/PrintStream;
       3: aload_0
       4: invokedynamic #36,  0             // InvokeDynamic #0:makeConcatWithConstants:(Ljava/lang/String;)Ljava/lang/String;
       9: invokevirtual #41                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      12: return
}
