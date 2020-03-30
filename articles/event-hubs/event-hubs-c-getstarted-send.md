---
title: 'Rövid útmutató: Események küldése C használatával – Azure Event Hubs'
description: 'Rövid útmutató: Ez a cikk egy forgatókönyvet biztosít egy C-alkalmazás létrehozásához, amely eseményeket küld az Azure Event Hubs számára.'
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
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 5bd4bb66b7e3c3ec37724f8684105befbc9132ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720672"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Rövid útmutató: Események küldése az Azure Event Hubs-ba c használatával

## <a name="introduction"></a>Bevezetés
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag bemutatja, hogyan küldhet eseményeket egy eseményközpontba egy C-ben lévő konzolalkalmazás használatával. 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A C fejlesztési környezet. Ez az oktatóanyag feltételezi, hogy a gcc verem egy Azure Linux Virtuális gép Ubuntu 14.04.This tutorial assumes the gcc stack on an Azure Linux VM with Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az okat a felügyeleti hitelesítő adatokat, amelyeket az alkalmazásnak az eseményközponttal való kommunikációhoz szüksége kell. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. Az eseményközpont hozzáférési kulcsának értékét a cikk utasításainak követésével szerezheti be: [Kapcsolati karakterlánc beírása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében írt kódban lévő hozzáférési kulcsot használja. Az alapértelmezett kulcsnév: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Kód írása az Eseményközpontokba küldött üzenetek küldéséhez
Ebben a szakaszban bemutatja, hogyan írhat egy C-alkalmazást az események nek az eseményközpontba küldéséhez. A kód az [Apache Qpid projekt](https://qpid.apache.org/)Proton AMQP könyvtárát használja. Ez hasonló a Service Bus-várólisták és témakörök C-ből a c-ből történő használatával, [amint az ebben a példában](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)látható. További információt a [Qpid Proton dokumentációjában](https://qpid.apache.org/proton/index.html)talál.

1. A [Qpid AMQP Messenger oldalon](https://qpid.apache.org/proton/messenger.html)kövesse a Qpid Proton telepítésének utasításait, a környezettől függően.
2. A Proton könyvtár fordításához telepítse a következő csomagokat:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Töltse le a [Qpid Proton könyvtárat,](https://qpid.apache.org/proton/index.html)és bontsa ki, pl.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Hozzon létre egy build könyvtárat, fordítsa le és telepítse:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Hozzon létre egy új fájlt, a **sender.c** fájlt a következő kóddal. Ne felejtse el lecserélni a SAS-kulcs/név, az eseményközpont neve és névtere értékeit. A kulcs URL-kódolású verzióját is be kell helyettesítenie a korábban létrehozott **SendRule-hoz.** Tudod URL-kódolni [itt](https://www.w3schools.com/tags/ref_urlencode.asp).
   
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
6. A fájl fordítása, feltételezve, **hogy gcc:**
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ez a kód egy 1-es kimenő ablakot használ az üzenetek mielőbbi kikényszerítéséhez. Javasoljuk, hogy az alkalmazás próbálja meg kötegelni az üzeneteket az átviteli-átviteli mivel. A [Qpid AMQP Messenger oldalon](https://qpid.apache.org/proton/messenger.html) megtudhatja, hogyan használhatja a Qpid Proton könyvtárat ebben és más környezetekben, valamint olyan platformokról, amelyekhez kötések állnak rendelkezésre (jelenleg Perl, PHP, Python és Ruby).

Futtassa az alkalmazást, hogy üzeneteket küldjön az eseményközpontba. 

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs szolgáltatásai és terminológiaei.](event-hubs-features.md)


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
