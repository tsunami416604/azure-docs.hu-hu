---
title: Az Azure Security Center for IoT Linux biztonsági ügynök hibaelhárítási útmutatója| Microsoft dokumentumok
description: Hibaelhárítás az Azure Security Center for IoT Security Agents for Linux használatával végzett munka elhancúrása.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600565"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Biztonsági ügynök hibaelhárítási útmutatója (Linux)

Ez a cikk bemutatja, hogyan oldhatja meg a biztonsági ügynök indítási folyamatának lehetséges problémáit.

Az Azure Security Center for IoT-ügynök önkiszolgáló indítása közvetlenül a telepítés után. Az ügynök indítási folyamat magában foglalja a helyi konfiguráció olvasása, az Azure IoT Hubhoz való csatlakozás, és a távoli ikerkonfiguráció beolvasása. Ha valamelyik lépést nem sikerül betenni, a biztonsági ügynök meghibásodhat.

Ebben a hibaelhárítási útmutatóban megtudhatja, hogyan:
> [!div class="checklist"]
> * Annak ellenőrzése, hogy fut-e a biztonsági ügynök
> * Biztonsági ügynökkel kapcsolatos hibák beszerezése
> * A biztonsági ügynökök hibáinak ismertetése és javítása 

## <a name="validate-if-the-security-agent-is-running"></a>Annak ellenőrzése, hogy fut-e a biztonsági ügynök

1. Az ellenőrzés a biztonsági ügynök fut, várjon néhány percet az ügynök telepítése után, és futtassa a következő parancsot. 
     <br>

    **C ügynök**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C# ügynök**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. Ha a parancs üres sort ad vissza, a biztonsági ügynök nem tudott sikeresen elindulni.    

## <a name="force-stop-the-security-agent"></a>A biztonsági ügynök kényszerítése 
Abban az esetben, ha a biztonsági ügynök nem tud elindulni, állítsa le az ügynököt a következő paranccsal, majd folytassa az alábbi hibatáblázattal:

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>Biztonsági ügynökkel kapcsolatos hibák beszerezése
1. A biztonsági ügynök hibáinak lekérése a következő parancs futtatásával:
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. A beolvasási biztonsági ügynök hibaparancs lekéri az Azure Security Center az IoT-ügynök által létrehozott összes naplót. Az alábbi táblázat segítségével megismerheti a hibákat, és megtartható a javításhoz szükséges lépések. 

> [!Note]
> A hibanaplók időrendi sorrendben jelennek meg. Győződjön meg róla, hogy vegye figyelembe az időbélyeg minden hiba, hogy segítse a szervizelést. 

## <a name="restart-the-agent"></a>Az ügynök újraindítása

1. A biztonsági ügynökkel kapcsolatos hiba megkeresése és javítása után próbálja meg újraindítani az ügynököt a következő parancs futtatásával. 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. Ismételje meg az előző folyamatot a leállítás lekéréséhez és a hibák beolvasásához, ha az ügynök továbbra is meghiúsul az indítási folyamaton. 

## <a name="understand-security-agent-errors"></a>A biztonsági ügynökhibáinak ismertetése

A biztonsági ügynök hibáinak többsége a következő formátumban jelenik meg: 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| Hibakód | Hiba alkód | A hiba részletei | C kiújulása | C kiújulása # |
|:-----------|:---------------|:--------|:------------|:------------|
| Helyi konfiguráció | Hiányzik a konfiguráció | Hiányzik egy konfiguráció a helyi konfigurációs fájlból. A hibaüzenetnek meg kell jelölnie, hogy melyik kulcs hiányzik. | Adja hozzá a hiányzó kulcsot a /var/LocalConfiguration.json fájlhoz, a részleteket a [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) oldalon találja.| Adja hozzá a hiányzó kulcsot a General.config fájlhoz, a részleteket lásd a [c#-localconfig-reference oldalon.](azure-iot-security-local-configuration-csharp.md) |
| Helyi konfiguráció | A nem imázselemzés konfigurációja | A konfigurációs érték nem elemezhető. A hibaüzenetnek meg kell jelölnie, hogy melyik kulcs nem elemezhető. A konfigurációs érték nem elemezhető, mert az érték nem a várt típusban van, vagy az érték tartományon kívül esik. | Javítsa ki a kulcs értékét a /var/LocalConfiguration.json fájlban úgy, hogy az megegyezik a LocalConfiguration sémával, a részleteket lásd a [c#-localconfig-reference című témakörben.](azure-iot-security-local-configuration-csharp.md) |  Javítsa ki a kulcs értékét a General.config fájlban úgy, hogy az megegyezik a sémával, a részleteket a [cs-localconfig-reference című témakörben](azure-iot-security-local-configuration-c.md) találja.|
| Helyi konfiguráció | Fájlformátum | Nem sikerült elemezni a konfigurációs fájlt. | A konfigurációs fájl sérült, töltse le az ügynököt, majd telepítse újra. | |
| Távoli konfiguráció | Időkorlát | Az ügynök nem tudta beolvasni az azureiotsecurity modul iker az időbeli eltöltött idő alatt. | Ellenőrizze, hogy a hitelesítési konfiguráció helyes-e, majd próbálkozzon újra. | Az ügynök nem tudta beolvasni az azureiotsecurity modul iker időbeli időn belül. | Ellenőrizze, hogy a hitelesítési konfiguráció helyes-e, majd próbálkozzon újra. |
| Hitelesítés | A fájl nem létezik | A megadott elérési úton lévő fájl nem létezik. | Győződjön meg arról, hogy a fájl megtalálható az adott elérési úton, vagy lépjen a **LocalConfiguration.json** fájlba, és módosítsa a **FilePath** konfigurációját. | Győződjön meg arról, hogy a fájl megtalálható az adott elérési úton, vagy lépjen a **Authentication.config** fájlba, és módosítsa a **filePath** konfigurációját.|
| Hitelesítés | Fájlengedély | Az ügynök nem rendelkezik a fájl megnyitásához szükséges engedélyekkel. | Adjon olvasási engedélyt az **asciotagent** felhasználónak az adott elérési úton lévő fájlra. | Ellenőrizze, hogy a fájl elérhető-e. |
| Hitelesítés | Fájlformátum | Az adott fájl formátuma nem megfelelő. | Ellenőrizze, hogy a fájl megfelelő formátumú-e. A támogatott fájltípusok a .pfx és a .pem. | Ellenőrizze, hogy a fájl érvényes tanúsítványfájl-e. |
| Hitelesítés | Nem engedélyezett | Az ügynök nem tudta hitelesíteni az IoT Hub a megadott hitelesítő adatokkal. | Ellenőrizze a hitelesítési konfigurációt a LocalConfiguration fájlban, menjen át a hitelesítési konfiguráción, és győződjön meg arról, hogy minden részlet helyes, ellenőrizze, hogy a fájl ban lévő titok megegyezik-e a hitelesített identitással. | Ellenőrizze a hitelesítési konfigurációt az Authentication.config fájlban, menjen át a hitelesítési konfiguráción, és győződjön meg arról, hogy minden részlet helyes, majd ellenőrizze, hogy a fájl ban lévő titok megegyezik-e a hitelesített identitással.
| Hitelesítés | Nem található | A készülék / modul található. | Hitelesítési konfiguráció ellenőrzése – győződjön meg arról, hogy a hostname helyes, az eszköz létezik az IoT Hubban, és rendelkezik egy azureiotsecurity ikermodullal. |  Hitelesítési konfiguráció ellenőrzése – győződjön meg arról, hogy a hostname helyes, az eszköz létezik az IoT Hubban, és rendelkezik egy azureiotsecurity ikermodullal. |
| Hitelesítés | Hiányzó konfiguráció | Hiányzik egy konfiguráció a *Authentication.config* fájlból. A hibaüzenetnek meg kell jelölnie, hogy melyik kulcs hiányzik. | Adja hozzá a hiányzó kulcsot a *LocalConfiguration.json* fájlhoz.| Adja hozzá a hiányzó kulcsot a *Authentication.config* fájlhoz, a részleteket lásd a [c#-localconfig-reference fájlban.](azure-iot-security-local-configuration-csharp.md) |
| Hitelesítés | A nem imázselemzés konfigurációja | A konfigurációs érték nem elemezhető. A hibaüzenetnek meg kell jelölnie, hogy melyik kulcs nem elemezhető. A konfigurációs érték nem elemezhető, mert vagy az érték nem a várt típusú, vagy az érték kívül esik a tartományon. |Javítsa ki a kulcs értékét a **LocalConfiguration.json** fájlban. |Javítsa ki a kulcs értékét a **Authentication.config** fájlban, hogy megfeleljen a sémának, a részleteket a [cs-localconfig-reference című témakörben](azure-iot-security-local-configuration-c.md) találja.|
|

## <a name="restart-the-agent"></a>Az ügynök újraindítása
1. A biztonsági ügynökkel kapcsolatos hiba megkeresése és javítása után indítsa újra az ügynököt a következő parancs futtatásával:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. Ha szükséges, ismételje meg az előző folyamatokat az ügynök leállításának kényszerítéséhez, és olvassa be a hibákat, ha az ügynök továbbra is sikertelen az indítási folyamaton. 

## <a name="next-steps"></a>További lépések
- Olvassa el az Azure Security Center for IoT szolgáltatás [áttekintését](overview.md)
- További információ az Azure Security Center for IoT [Architecture szolgáltatásról](architecture.md)
- Az Azure Security Center [ioT-szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- Olvassa el az Azure Security Center for IoT szolgáltatás [gyakori kérdések](resources-frequently-asked-questions.md)
- További információ a [nyers biztonsági adatok](how-to-security-data-access.md) eléréséről
- Az [ajánlások megismerése](concept-recommendations.md)
- A biztonsági [riasztások ismertetése](concept-security-alerts.md)
