mvn clean install
java -cp my-app-client/target/my-app-client-0.1-SNAPSHOT.jar:my-app/target/my-app-0.1-SNAPSHOT.jar:my-new-base/target/my-base-0.2-SNAPSHOT.jar:my-old-base/target/my-base-0.1-SNAPSHOT.jar com.bawi.AppClient

Hello app!
Exception in thread "main" java.lang.NoSuchMethodError: 'void com.bawi.base.Util.print(java.lang.String)'
        at com.bawi.App.print(App.java:8)
        at com.bawi.AppClient.main(AppClient.java:5)


mvn clean install -Pshade
java -cp my-app-client/target/my-app-client-0.1-SNAPSHOT.jar:my-app/target/my-app-0.1-SNAPSHOT.jar:my-new-base/target/my-base-0.2-SNAPSHOT.jar com.bawi.AppClient

Hello app!
Hello world! Bartek


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

