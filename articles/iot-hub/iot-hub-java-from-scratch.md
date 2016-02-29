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

這篇文章說明如何建立用戶端應用程式使用 [Java 的 Microsoft Azure IoT 裝置 SDK][lnk-java-sdk] 與 Azure IoT 中心進行通訊。 本教學課程說明如何建立和建置的專案使用 [Maven][apache-maven] 工具。 這些指示一體適用於 Windows 或 Linux 電腦。

您可以檢視 [Java API 文件][lnk-java-api-docs] 供您參考。

## 安裝

請參閱 [準備開發環境][devbox-setup] 先決條件和開發環境在 Windows 或 Linux 上設定的詳細資訊。

> [AZURE.NOTE] 請務必完成中的步驟 [準備開發環境][devbox-setup] 在開始本教學課程安裝必要元件，並將所需的 JAR 檔案新增至本機 Maven 儲存機制之前。

## 建立專案

1. 在命令列工具中執行下列命令，以在開發電腦的適當位置上，建立新的空白 Maven 專案：

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=iot-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [AZURE.NOTE] 這是單一、 完整的命令。 若要將其貼至命令列工具，請務必複製完整的命令。

    此命令會建立名為的專案資料夾 *iot 裝置* 具有標準的 Maven 專案結構。 如需詳細資訊，請參閱 [Maven 在 5 分鐘內][maven-five-minutes] Apache 網站上。

2. 開啟 **pom.xml** iot 裝置資料夾在文字編輯器中的檔案。

3. 新增下列新 **相依性** 之後要包含必要的用戶端程式庫的現有區段:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-client</artifactId>
      <version>1.0.0-preview.4</version>
    </dependency>
    ```

4. 新增下列 **建置** 區段之後 **相依性** 區段，讓最終 JAR 檔案包含相依性和資訊清單識別 **主要** 類別。

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

1. 開啟 **App.java** iot 裝置/src/main/java/com/mycompany/應用程式資料夾在文字編輯器中的檔案。

2. 新增下列 **匯入** 陳述式，包括 IoT 裝置程式庫之後, **封裝** 陳述式:

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

3. 新增下列 **EventCallback** 類別內的巢狀類別 **應用程式** 類別。  **執行** 方法中的 **EventCallback** ，裝置會接收認可從裝置對雲端訊息的回應中的 IoT 中心時，會叫用類別。

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

4. 新增下列 **MessageCallback** 類別內的巢狀類別 **應用程式** 類別。  **執行** 方法中的 **MessageCallback** 類別可讓您的意見反應訊息傳送至您的 IoT 中心定域機組對裝置訊息傳送到裝置的回應。

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
  - 開啟 **DeviceClient** 裝置對雲端訊息傳送和接收定域機組對裝置訊息加以啟用。
  - 傳送範例訊息給您的 IoT 中樞。

    以有效的裝置連接字串取代 `<your device connection string>`。 您可以佈建裝置，並擷取其使用的連接字串 [DeviceExplorer][lnk-device-explorer] 工具或 [IoT 中心總管][lnk-iothub-explorer] 工具。

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

6. 若要編譯程式碼，並封裝成 JAR 檔案，執行下列命令在命令提示字元中 **iot 裝置** 專案資料夾:

    ```
    mvn package
    ```

7. 若要執行應用程式，執行下列命令在命令提示字元中 **iot 裝置** 專案資料夾:

    ```
    java -jar target/iot-device-1.0-SNAPSHOT-with-deps.jar
    ```

8. 您可以使用 [DeviceExplorer][lnk-device-explorer] 工具來監視 IoT 中心接收裝置對雲端訊息，並從 IoT 中心，將定域機組-裝置訊息傳送至您的裝置。

## 變更記錄詳細程度

若要變更記錄詳細程式，請在您 `config.properties` 檔案中加入下行：

```
.level = {LOGGING_LEVEL}
```

> [AZURE.NOTE]  您可以閱讀說明不同的 [記錄層級] 
(http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html)。

接著設定 JVM 屬性 `java.util.logging.config.file={Path to your config.properties file}`。

若要記錄 AMQP 框架，請在您的命令環境中設定環境變數 `PN_TRACE_FRM=1`。


[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-java-api-docs]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[apache-maven]: https://maven.apache.org/index.html
[maven-five-minutes]: https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
[devbox-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md
[lnk-iothub-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/doc/provision_device.md


