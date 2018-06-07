---
title: Események küldése az Azure Event Hubs C használatával |} Microsoft Docs
description: Események küldése az Azure Event Hubs C használatával
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 12/4/2017
ms.author: sethm
ms.openlocfilehash: e3267b54fa0c8593e0f9366c009656f36e4094ef
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807815"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Események küldése az Azure Event Hubs C használatával

## <a name="introduction"></a>Bevezetés
Az Event Hubs egy kiválóan méretezhető fogadórendszer, amely is több millió eseményt másodpercenként, az alkalmazás engedélyezése feldolgozni, és elemezze a nagy mennyiségű adatot a csatlakoztatott eszközök és alkalmazások által létrehozott. Miután egy eseményközpontba való összegyűjtését, átalakítás és tárolására is használható adatok bármilyen valós idejű elemzési szolgáltató vagy tárolási fürt használatával.

További információkért lásd: a [Event Hubs – áttekintés](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview).

Ez az oktatóanyag ismerteti, hogyan lehet események küldése az event hubs egy konzolalkalmazás használatával a c kiszolgálóra. További események fogadása, kattintson a bal oldali tartalomjegyzék a megfelelő fogadó nyelvet.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A C környezet. Ez az oktatóanyag azt feltételezi, hogy az Azure Linux virtuális gép az Ubuntu 14.04 ÖET verembe.
* [A Microsoft Visual Studio](https://www.visualstudio.com/).
* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Üzenetek küldése az Event Hubs szolgáltatásnak
Ebben a szakaszban is küldi az eseményeket az eseményközpontjába C alkalmazások írásához mutatja be. A kódot használja a Proton AMQP kódtárat a [Apache Qpid projekt](http://qpid.apache.org/). Ez hasonló használata a Service Bus-üzenetsorok és témakörök AMQP c látható az [Ez a példa](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). További információkért lásd: a [Qpid Proton dokumentáció](http://qpid.apache.org/proton/index.html).

1. Az a [Qpid AMQP Messenger lap](https://qpid.apache.org/proton/messenger.html), kövesse az utasításokat a környezettől függően Qpid Proton telepítéséhez.
2. Fordítása a Proton könyvtárban, a következő csomagok telepítése:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Töltse le a [Qpid Proton könyvtár](http://qpid.apache.org/proton/index.html), és bontsa ki, pl.:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Hozzon létre egy build directory, a fordítás és a telepítés:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Munka címtárat, hozzon létre egy új fájlt nevű **sender.c** a következő kóddal. Ne felejtse el lecserélni a SAS/kulcsnév, az eseményközpont neveként és a névtér értékeit. Is helyettesítse be a kulcsot egy URL-kódolású verziója a **SendRule** korábban létrehozott. Beállíthatja az URL-kódolja azt [Itt](http://www.w3schools.com/tags/ref_urlencode.asp).
   
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
6. A fájlt, feltéve, hogy **ÖET**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ez a kód egy kimenő 1-ablakban kimenő üzenetek minél hamarabb kényszerítése használja. Javasoljuk, hogy az alkalmazás megpróbálja kötegelt üzenetek növelheti a teljesítményt. Tekintse meg a [Qpid AMQP Messenger lap](https://qpid.apache.org/proton/messenger.html) olvashat ebben és más környezetekben, illetve a platformok, amelynek kötések találhatók Qpid Proton tár használatára (jelenleg Perl, PHP, Python vagy Ruby).


## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
