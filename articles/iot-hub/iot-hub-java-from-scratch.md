<properties
    pageTitle="建立 IoT 中樞 Java 用戶端 |Microsoft Azure"
    description="依照本教學課程的說明，建置使用適用於 Java 之 Microsoft Azure IoT 裝置 SDK IoT 中樞 的 Java* 用戶端，以與 Azure IoT 中樞通訊。"
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/26/2015"
     ms.author="dobett"/>


# 使用 Java 建立 Azure IoT 中樞用戶端應用程式

這篇文章說明如何建立用戶端應用程式使用 [Microsoft Azure IoT 裝置 SDK for Java ][lnk-java-sdk] Azure IoT 中心與通訊。 本教學課程說明如何建立和建置的專案使用 [Maven ][apache-maven] 工具。 這些指示一體適用於 Windows 或 Linux 電腦。

您可以檢視 [Java API 文件 ][lnk-java-api-docs] 供您參考。

## 安裝

請參閱 [準備開發環境 ][devbox-setup] 先決條件和開發環境在 Windows 或 Linux 上設定的詳細資訊。
> [AZURE.NOTE] 請務必完成中的步驟 [準備開發環境 ][devbox-setup] 在開始本教學課程安裝必要元件，並將所需的 JAR 檔案新增至本機 Maven 儲存機制之前。

## 建立專案

1. 在命令列工具中執行下列命令，以在開發電腦的適當位置上，建立新的空白 Maven 專案：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE] 這只是一個非常長的命令。 若要將其貼至命令列工具，請務必複製完整的命令。

    此命令會建立具有標準 Maven 專案結構，名為 *iot-device* 的專案資料夾。 如需詳細資訊，請參閱 [Maven 在 5 分鐘內 ][maven-five-minutes] Apache 網站上。

2. 在文字編輯器中，開啟 iot-device 資料夾中的 **pom.xml** 檔案。

3. 在現有的 **dependency** 區段之後新增下列 dependency 區段，以加入必要的用戶端程式庫：

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. 在 **dependencies** 區段之後新增下列 **build** 區段，以便於最終的 JAR 檔案能夠包含可以指出 **main** 類別的相依性與資訊清單。

    ```
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-shade-plugin</artifactId>
          <executions>
            <execution>
              <phase>package</phase>
              <goals>
                <goal>shade</goal>
              </goals>
            </execution>
          </executions>
          <configuration>
            <finalName>${artifactId}-${version}-with-deps</finalName>
          </configuration>
        </plugin>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-jar-plugin</artifactId>
          <version>2.6</version>
          <configuration>
            <archive>
              <manifest>
                <addClasspath>true</addClasspath>
                <mainClass>com.mycompany.app.App</mainClass>
              </manifest>
            </archive>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. 儲存 **pom.xml** 檔案。

## 建立應用程式

1. 在文字編輯器中，開啟 iot-device/src/main/java/com/mycompany/app 資料夾中的 **App.java** 檔案。

2. 新增下列 **import** 陳述式 (已在  **package** 陳述式後加入 IoT 裝置程式庫)：

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Scanner;

    import javax.naming.SizeLimitExceededException;
    ```

3. 在 **App** 類別中新增下列 **EventCallback** 巢狀類別。 當裝置從您的 IoT 中樞收到裝置對雲端訊息的回應認可時，會叫用 **EventCallback** 類別中的 **execute** 方法。

    ```
    protected static class EventCallback
        implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        Integer i = (Integer) context;
        System.out.println("IoT Hub responded to message " + i.toString()
            + " with status " + status.name());
      }
    }
    ```

4. 在 **App** 類別中新增下列 **MessageCallback** 巢狀類別。 **MessageCallback** 類別中的 **execute** 方法可讓您的將意見反應訊息傳送給您的 IoT 中樞，做為雲端對裝置訊息由裝置接收。

    ```
    protected static class MessageCallback
        implements com.microsoft.azure.iothub.MessageCallback
    {
      public IotHubMessageResult execute(Message msg,
          Object context)
      {
        System.out.println(
            "Received message with content: " + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

          return IotHubMessageResult.COMPLETE;
      }
    }
    ```

5. 取代現有 **主要** 方法，以下列程式碼:

  - 建立 **DeviceClient** 執行個體。
  - 初始化雲端對裝置訊息的訊息回撥。
  - 開啟 **DeviceClient**，使其能夠傳送裝置對雲端訊息及接收雲端對對裝置訊息。
  - 傳送範例訊息給您的 IoT 中樞。

    取代 `< 您裝置的連接字串 >` 與有效的裝置的連接字串。您可以佈建裝置，並擷取其使用的連接字串 [DeviceExplorer ][lnk-device-explorer] 工具或 [IoT 中心總管 ][lnk-iothub-explorer] 工具。

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException
    {
      String connString = "<your device connection string>";
      IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;

      DeviceClient client = new DeviceClient(connString, protocol);

      MessageCallback messageCallback = new MessageCallback();
      client.setMessageCallback(messageCallback, null);

      client.open();

      for (int i = 0; i < 10; ++i)
      {
        String msgStr = "Event Message " + Integer.toString(i);
        try
        {
          Message msg = new Message(msgStr);
          msg.setProperty("messageCount", Integer.toString(i));
          System.out.println(msgStr);

          EventCallback eventCallback = new EventCallback();
          client.sendEventAsync(msg, eventCallback, i);
        }
        catch (Exception e)
        {
        }
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();

      client.close();
    }
    ```

6. 若要編譯程式碼並將其封裝成 JAR 檔案，請在命令提示字元上，於 **iot-device** 專案資料夾中執行下列命令：

    ```
    mvn package
    ```

7. 若要執行應用程式，請在命令提示字元上，於 **iot-device** 專案資料夾中執行下列命令：

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. 您可以使用 [DeviceExplorer ][lnk-device-explorer] 工具來監視 IoT 中心接收裝置對雲端訊息，並從 IoT 中心，將定域機組-裝置訊息傳送至您的裝置。

## 變更記錄詳細程度

若要變更的記錄資料粒度，包括在下行程式 `config.properties` 檔案:

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]  您可以閱讀說明不同的 [記錄層級] 
(http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html)。

然後，將 JVM 屬性 `java.util.logging.config.file={Path config.properties 檔案}`。

若要記錄 AMQP 框架，設定環境變數 `PN_TRACE_FRM = 1` 命令環境中。



[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md 
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html 
[apache-maven]: https://maven.apache.org/index.html 
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html 
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md 
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md 
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md 

