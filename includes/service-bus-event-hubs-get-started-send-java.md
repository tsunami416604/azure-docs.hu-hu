## 將訊息傳送至事件中心

在本節中，我們會撰寫一個 Java 主控台應用程式，以將事件傳送至事件中樞。 我們利用 JMS AMQP 提供者從 [Apache Qpid 專案](http://qpid.apache.org/)。 如所示，這是類似於使用服務匯流排佇列和主題與 AMQP， [這裡](../service-bus/service-bus-java-how-to-use-jms-api-amqp.md)。 如需詳細資訊，請參閱 [Qpid JMS 文件](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) 和 [Java 訊息服務](http://www.oracle.com/technetwork/java/jms/index.html)。

1. 在 Eclipse 中，安裝 [Azure Toolkit for Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx)。 這包括 Qpid JMS AMQP 用戶端程式庫。

2. 在 Eclipse 中，建立名為的新 Java 專案 **寄件者**。

3. 在 Eclipse 封裝總管] 中，以滑鼠右鍵按一下 **寄件者** 專案，然後選取 **屬性**。 在對話方塊的左窗格中，按一下 [ **Java 組建路徑**, ，然後按一下 [ **程式庫** ] 索引標籤，然後 **新增程式庫** ] 按鈕。 選取 **Apache Qpid Client Libraries for JMS （由 MS Open Tech) 套件**, ，按一下 [ **下一步**, ，然後按一下 [ **完成**。

    ![][8]

4. 建立名為 **servicebus.properties** 根目錄中的 **寄件者** 專案中的，使用下列內容。 請記得將值取代為您的：
    - 事件中樞名稱。
    - 命名空間名稱 (後者通常是 `{event hub name}-ns`)。
    - URL 編碼 **SendRule** （您記下這個機碼時建立事件中樞） 的索引鍵。 您可以進行 URL 編碼它 [這裡](http://www.w3schools.com/tags/ref_urlencode.asp)。

            # servicebus.properties - sample JNDI configuration

            # Register a ConnectionFactory in JNDI using the form:
            # connectionfactory.[jndi_name] = [ConnectionURL]
            connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

            # Register some queues in JNDI using the form
            # queue.[jndi_name] = [physical_name]
            # topic.[jndi_name] = [physical_name]
            queue.EventHub = {event hub name}

5. 建立新的類別，名為 **寄件者**。 新增下列 `import` 陳述式：

        import java.io.BufferedReader;
        import java.io.IOException;
        import java.io.InputStreamReader;
        import java.io.UnsupportedEncodingException;
        import java.util.Hashtable;

        import javax.jms.BytesMessage;
        import javax.jms.Connection;
        import javax.jms.ConnectionFactory;
        import javax.jms.Destination;
        import javax.jms.JMSException;
        import javax.jms.MessageProducer;
        import javax.jms.Session;
        import javax.naming.Context;
        import javax.naming.InitialContext;
        import javax.naming.NamingException;

6. 然後，在其中新增下列程式碼：

        public static void main(String[] args) throws NamingException,
                JMSException, IOException, InterruptedException {
            // Configure JNDI environment
            Hashtable<String, String> env = new Hashtable<String, String>();
            env.put(Context.INITIAL_CONTEXT_FACTORY,
                    "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
            env.put(Context.PROVIDER_URL, "servicebus.properties");
            Context context = new InitialContext(env);

            ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

            Destination queue = (Destination) context.lookup("EventHub");

            // Create Connection
            Connection connection = cf.createConnection();

            // Create sender-side Session and MessageProducer
            Session sendSession = connection.createSession(false,
                    Session.AUTO_ACKNOWLEDGE);
            MessageProducer sender = sendSession.createProducer(queue);

            System.out.println("Press Ctrl-C to stop the sender process");
            System.out.println("Press Enter to start now");
            BufferedReader commandLine = new java.io.BufferedReader(
                    new InputStreamReader(System.in));
            commandLine.readLine();

            while (true) {
                sendBytesMessage(sendSession, sender);
                Thread.sleep(200);
            }
        }

        private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
            BytesMessage message = sendSession.createBytesMessage();
            message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
            sender.send(message);
            System.out.println("Sent message");
        }



<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png


