---
title: Hibaelhárítás – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Hibaelhárítás az IOt Edge Eseményrácsban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100160"
---
# <a name="common-issues"></a>Gyakori problémák

Ha problémákat tapasztal az Azure Event Grid használatával az IoT Edge-en a környezetében, használja ezt a cikket útmutatóként a hibaelhárításhoz és a megoldáshoz.

## <a name="view-event-grid-module-logs"></a>Eseményrács modulnaplóinak megtekintése

A hibaelhárításhoz szükség lehet az Event Grid modul naplóinak elérésére. Ehhez a virtuális gép, ahol a modul telepítve van, futtassa a következő parancsot:

Windows rendszerben a

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Linux alatt,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>Nem lehet HTTPS-kérelmeket betartani

* Először győződjön meg arról, hogy az Event Grid modul **bejövő:serverAuth:tlsPolicy** **beállítása szigorú** vagy **engedélyezve van.**

* Ha a modul-modul kommunikáció, győződjön meg arról, hogy a hívás a **4438-as** porton, és a modul neve megegyezik a telepített. 

  Például ha az Event Grid modul neve **eventgridmodule** néven lett **https://eventgridmodule:4438**telepítve, akkor az URL-címet kell . Ellenőrizze, hogy a ház és a port száma helyes-e.
    
* Ha nem IoT-modulból származik, győződjön meg arról, hogy az Event Grid port le van képezve a gazdagépre az üzembe helyezés során, például

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>Nem lehet HTTP-kérelmeket letenni

* Először győződjön meg arról, hogy az Event Grid modul **bejövő:serverAuth:tlsPolicy** beállítása **engedélyezve** vagy **letiltva.**

* Ha a modul-modul kommunikáció, győződjön meg arról, hogy a hívás a port **5888** és a modul neve megegyezik a telepített. 

  Például ha az Event Grid modul neve **eventgridmodule** néven lett **http://eventgridmodule:5888**telepítve, akkor az URL-címet kell . Ellenőrizze, hogy a ház és a port száma helyes-e.
    
* Ha nem IoT-modulból származik, győződjön meg arról, hogy az Event Grid port le van képezve a gazdagépre az üzembe helyezés során, például

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>A tanúsítványláncot nem megbízható hatóság bocsátotta ki

Alapértelmezés szerint az Event Grid modul úgy van konfigurálva, hogy hitelesítse az ügyfeleket az IoT Edge biztonsági démon által kiadott tanúsítvánnyal. Győződjön meg arról, hogy az ügyfél olyan tanúsítványt mutat be, amely ehhez a lánchoz gyökerezik.

**IoTSecurity** osztály [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) bemutatja, hogyan lehet lekérni a tanúsítványokat az IoT Edge biztonsági démon, és ezzel konfigurálja a kimenő hívásokat.

Ha nem éles környezetben, akkor lehetősége van kikapcsolni az ügyfélhitelesítést. Ennek módjáról a [Biztonság és hitelesítés](security-authentication.md) című dokumentumban tájékodhat.

## <a name="debug-events-not-received-by-subscriber"></a>Az előfizető által nem fogadott hibakeresési események

Ennek tipikus okai a következők:

* Az esemény soha nem volt sikeresen közzé. 200-as HTTP-statuscode(OK) protokollt kell kapni, amikor eseményt tesz közzé az Event Grid modulba.

* Ellenőrizze az esemény-előfizetést a következők ellenőrzéséhez:
    * A végpont URL-címe érvényes
    * Az előfizetés ben lévő szűrők nem okozzák az esemény "eldobását".

* Annak ellenőrzése, hogy fut-e az előfizetői modul

* Jelentkezzen be a virtuális gépre, ahol az Event Grid modul telepítve van, és tekintse meg a naplókat.

* Kapcsolja be a kézbesítési naplózást a **broker:logDeliverySuccess=true** beállításával és az Event Grid modul újratelepítésével és a kérelem újbóli megkísérlésével. A kézbesítésenkénti naplózás bekapcsolása hatással lehet az átviteli és a késésre, így a hibakeresés befejezése után azt javasoljuk, hogy ezt visszakell állítani **a broker:logDeliverySuccess=false-ra,** és újraüzembe helyezzük az Event Grid modult.

* Kapcsolja be a metrikákat **a metrikák:reportertype=console** beállításával és az Event Grid modul újratelepítésével. Az ezt követő műveletek eredményeképpen metrikák vannak bejelentkezve az Event Grid modul konzolján, amely további hibakeresésre használható. Javasoljuk, hogy kapcsolja be a metrikák csak a hibakeresés, és miután befejezte, hogy kapcsolja ki a **metrikák:reportertype=none** és az Event Grid modul újratelepítése.

## <a name="next-steps"></a>További lépések

Jelentse az Event Grid ioT Edge-en [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)való használatával kapcsolatos problémákat, javaslatokat a alkalmazásban.