---
title: Események küldése az Azure Event Hubs C használatával |} A Microsoft Docs
description: Események küldése az Azure Event Hubs C használatával
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 262f274541f486f5457ef8eae6fd4f60fb34824e
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055115"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Események küldése az Azure Event Hubs C használatával

## <a name="introduction"></a>Bevezetés
Event Hubs szolgáltatás egy kiválóan méretezhető fogadórendszer, amely képes másodpercenként több millió feldolgozására, ezáltal az alkalmazások feldolgozásához, és a csatlakoztatott eszközök és alkalmazások által létrehozott hatalmas adatmennyiségek elemzését. Miután az adatoknak egy eseményközpontba való összegyűjtését, átalakíthatja és adatok bármilyen valós idejű elemzési szolgáltató vagy tárolási fürt használatával tárolhatja.

További információkért tekintse meg a [Event Hubs – áttekintés](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview).

Ez az oktatóanyag azt ismerteti, hogyan küldhet eseményeket egy eseményközpontba egy konzolalkalmazás a c használatával Fogadott események kapcsolatos további információkért kattintson a megfelelő fogadónyelvre a bal oldali tartalomjegyzékben.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* C-fejlesztési környezetre. Ez az oktatóanyag feltételezi, hogy a gcc-verem Ubuntu 14.04 Azure Linux rendszerű virtuális Gépekhez.
* [A Microsoft Visual Studio](https://www.visualstudio.com/).
* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Üzenetek küldése az Event Hubs szolgáltatásnak
Ez a rész bemutatja, hogyan küldhet eseményeket az eseményközpontjába C alkalmazások írásához. A kód a Proton AMQP kódtárat használja a [Apache Qpid projekt](http://qpid.apache.org/). Ez hasonló a Service Bus-üzenetsorok és témakörök az amqp-vel a C látható módon a [ebben a példában](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). További információkért lásd: a [Qpid Proton dokumentáció](http://qpid.apache.org/proton/index.html).

1. Az a [Qpid AMQP Messenger lap](https://qpid.apache.org/proton/messenger.html), kövesse az utasításokat követve Qpid Proton, telepítse a környezettől függően.
2. A Proton könyvtár fordítása, telepítse a következő csomagokat:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Töltse le a [Qpid Proton könyvtár](http://qpid.apache.org/proton/index.html), és csomagolja ki, például:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Hozzon létre egy build könyvtárat, fordítás, és telepítse:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. A munkahelyi könyvtárban hozzon létre egy új fájlt **sender.c** az alábbi kódra. Ne feledje el az értékeket a SAS/kulcsnév, az eseményközpont neve és a névtér. Továbbá cserélje le a kulcs URL-kódolású verzióját a **SendRule** korábban létrehozott. URL-kódolása is azt [Itt](http://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
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
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
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
6. Fordítsa le a fájlt, feltéve, hogy **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ezt a kódot egy kimenő ablakban 1 használja az üzenetek meg minél hamarabb kényszerítése. Javasoljuk, hogy az alkalmazás próbál kötegelt üzenetek átviteli sebesség növelése érdekében. Tekintse meg a [Qpid AMQP Messenger lap](https://qpid.apache.org/proton/messenger.html) ezzel és más környezetekben, és platformokon, amelynek kötések vannak megadva a Qpid Proton library használatával kapcsolatos információk (jelenleg a Perl, a PHP, Python vagy Ruby).


## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
