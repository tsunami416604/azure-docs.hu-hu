## 將訊息傳送至事件中心

在本節中，我們會撰寫一個 C 應用程式，以將事件傳送至事件中心。 我們將使用中的 Proton AMQP 程式庫 [Apache Qpid 專案](http://qpid.apache.org/)。 這相當於使用服務匯流排佇列和主題與 AMQP 透過 C 示 [這裡](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)。 如需詳細資訊，請參閱 [Qpid Proton 文件](http://qpid.apache.org/proton/index.html)。

1. 從 [Qpid AMQP Messenger 頁面](http://qpid.apache.org/components/messenger/index.html), ，按一下 [ **安裝 Qpid Proton** 連結，並遵循的指示，根據您的環境。 我們將假設 Linux 環境中。例如， [Azure Linux VM](../articles/virtual-machines/virtual-machines-linux-tutorial.md) 有 Ubuntu 14.04。

2. 若要編譯 Proton 程式庫，請安裝下列封裝：

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. 下載 [Qpid Proton 程式庫](http://qpid.apache.org/proton/index.html) 程式庫，並將它解壓縮，例如 ︰

    ```
    wget http://apache.fastbull.org/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. 建立組建目錄、編譯並安裝：

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. 在工作目錄中，建立新的檔案稱為 **sender.c** 含有下列內容。 請務必替代您事件中樞名稱和命名空間名稱的值 (後者通常是 `{event hub name}-ns`)。 您也必須替代的索引鍵的 URL 編碼版本 **SendRule** 稍早建立。 您可以進行 URL 編碼它 [這裡](http://www.w3schools.com/tags/ref_urlencode.asp)。

    ```
    #include "proton/message.h"
    #include "proton/messenger.h"
    
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
    
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }  
    
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
    
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
    
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
    
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
    
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
    
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
    
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
    
        pn_message_free(message);
    }
    
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
    
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
    
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
    
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
    
        return 0;
    }
    ```

6. 編譯檔案，假設 **gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] 在此程式碼中，我們用於輸出視窗 1 盡快強制輸出訊息。 應用程式一般應該會嘗試批次處理訊息，以增加輸送量。 請參閱 [Qpid AMQP Messenger 頁面](http://qpid.apache.org/components/messenger/index.html) 如需有關如何使用 Qpid Proton 程式庫，這和其他環境中以及從提供繫結的平台 （目前是 Perl、 PHP、 Python 和 Ruby）。


