---
title: A biztonsági ügynök indítási fellépéseinek hibakeresése (Linux)
description: A Linux rendszerhez készült IoT biztonsági ügynökökkel kapcsolatos Azure Security Center használata.
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
ms.openlocfilehash: 935a99dd34b0a4e3d4970e8d91f9332d2bc1489a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310570"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Biztonsági ügynök hibaelhárítási útmutatója (Linux)

Ez a cikk azt ismerteti, Hogyan oldhatók meg a biztonsági ügynök indítási folyamatával kapcsolatos lehetséges problémák.

A IoT-ügynök önkiszolgáló Azure Security Center a telepítés után azonnal elindul. Az ügynök indítási folyamata magában foglalja a helyi konfiguráció olvasását, az Azure-IoT Hub való csatlakozást és a távoli Twin konfiguráció beolvasását. A fenti lépések bármelyikének meghibásodása esetén a biztonsági ügynök sikertelen lehet.

Ez a hibaelhárítási útmutató a következőket ismerteti:

> [!div class="checklist"]
> * Annak ellenőrzése, hogy fut-e a biztonsági ügynök
> * Biztonsági ügynök hibáinak beolvasása
> * A biztonsági ügynökkel kapcsolatos hibák megismerése és szervizelése

## <a name="validate-if-the-security-agent-is-running"></a>Annak ellenőrzése, hogy fut-e a biztonsági ügynök

1. Annak ellenőrzéséhez, hogy a biztonsági ügynök fut-e, várjon néhány percet az ügynök telepítése után, és futtassa a következő parancsot.
     <br>

    **C ügynök**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **C#-ügynök**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Ha a parancs üres sort ad vissza, a biztonsági ügynök nem tudott sikeresen elindulni.

## <a name="force-stop-the-security-agent"></a>Biztonsági ügynök leállításának kényszerítése

Abban az esetben, ha a biztonsági ügynök nem indul el, állítsa le az ügynököt a következő paranccsal, majd folytassa az alábbi hibaüzenettel:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Biztonsági ügynök hibáinak beolvasása

1. A biztonsági ügynök hibája (i) beolvasása a következő parancs futtatásával:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. A biztonsági ügynök beolvasása parancs lekérdezi a IoT-ügynök Azure Security Center által létrehozott összes naplót. A következő táblázat segítségével megismerheti a hibákat, és megteheti a szervizeléshez szükséges megfelelő lépéseket.

> [!Note]
> A hibanapló időrendi sorrendben jelennek meg. Ügyeljen arra, hogy az egyes hibák timestampjét a szervizelés elősegítése érdekében jegyezze fel.

## <a name="restart-the-agent"></a>Az ügynök újraindítása

1. A biztonsági ügynök hibájának megkeresése és javítása után próbálja meg újraindítani az ügynököt a következő parancs futtatásával.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Ismételje meg az előző folyamatot a Leállítás lekéréséhez, és a hibák beolvasásához, ha az ügynök továbbra is az indítási folyamat során leáll.

## <a name="understand-security-agent-errors"></a>A biztonsági ügynökkel kapcsolatos hibák ismertetése

A biztonsági ügynök legtöbb hibája a következő formátumban jelenik meg:

```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Hibakód | Hiba alkódja | A hiba részletei | C szervizelése | C szervizelése # |
|:-----------|:---------------|:--------|:------------|:------------|
| Helyi konfiguráció | Hiányzó konfiguráció | Hiányzik egy konfiguráció a helyi konfigurációs fájlból. A hibaüzenetnek meg kell jelennie, hogy melyik kulcs hiányzik. | Adja hozzá a hiányzó kulcsot a/var/LocalConfiguration.json-fájlhoz. a részletekért tekintse meg a [cs-localconfig-Reference](azure-iot-security-local-configuration-c.md) című témakört.| Adja hozzá a hiányzó kulcsot az általános. config fájlhoz a részletekért lásd a [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) című témakört. |
| Helyi konfiguráció | Nem értelmezhető konfiguráció | A konfigurációs érték nem elemezhető. A hibaüzenetnek azt kell megadnia, hogy melyik kulcsot nem lehet elemezni. A konfigurációs érték nem elemezhető, mert az érték nem a várt típusú, vagy az érték a tartományon kívül esik. | Javítsa ki a kulcs értékét a/var/LocalConfiguration.json fájlban, hogy az megfeleljen a LocalConfiguration sémának, lásd a [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) című témakört a részletekért. |  Javítsa ki a kulcs értékét az általános. config fájlban, hogy az megfeleljen a sémának, tekintse meg a [cs-localconfig-Reference](azure-iot-security-local-configuration-c.md) című részt a részletekért.|
| Helyi konfiguráció | Fájlformátum | Nem sikerült elemezni a konfigurációs fájlt. | A konfigurációs fájl sérült, töltse le az ügynököt, és telepítse újra. | |
| Távoli konfiguráció | Időkorlát | Az ügynök nem tudja beolvasni a azureiotsecurity modult az időtúllépési időszakon belül. | Győződjön meg arról, hogy a hitelesítési konfiguráció helyes, és próbálkozzon újra. | Az ügynök nem tudott beolvasni az azureiotsecurity modult az időtúllépési időszakon belül. | Győződjön meg arról, hogy a hitelesítési konfiguráció helyes, és próbálkozzon újra. |
| Hitelesítés | A fájl nem létezik | A megadott elérési úton található fájl nem létezik. | Győződjön meg arról, hogy a fájl létezik a megadott elérési úton, vagy nyissa meg a **LocalConfiguration. JSON** fájlt, és módosítsa a **filepath** konfigurációt. | Győződjön meg arról, hogy a fájl létezik a megadott elérési úton, vagy nyissa meg a **Authentication. config** fájlt, és módosítsa a **filepath** konfigurációját.|
| Hitelesítés | Fájl engedélye | Az ügynök nem rendelkezik megfelelő engedélyekkel a fájl megnyitásához. | A megadott elérési úton adja meg a **asciotagent** -felhasználó olvasási engedélyeit a fájlhoz. | Győződjön meg arról, hogy a fájl elérhető. |
| Hitelesítés | Fájlformátum | A megadott fájl formátuma nem megfelelő. | Győződjön meg arról, hogy a fájl formátuma megfelelő. A támogatott fájltípusok:. pfx és. PEM. | Győződjön meg arról, hogy a fájl érvényes tanúsítványfájl. |
| Hitelesítés | Nem engedélyezett | Az ügynök nem tudta hitelesíteni a IoT Hub a megadott hitelesítő adatokkal. | Ellenőrizze a hitelesítési konfigurációt a LocalConfiguration fájlban, hajtsa végre a hitelesítési konfigurációt, és győződjön meg róla, hogy az összes adat helyes, ellenőrizze, hogy a fájl titkos kulcsa megegyezik-e a hitelesített identitással. | Ellenőrizze a hitelesítési konfigurációt a Authentication. config fájlban, hajtsa végre a hitelesítési konfigurációt, és győződjön meg róla, hogy az összes adat helyes, majd ellenőrizze, hogy a fájl titkos kulcsa megegyezik-e a hitelesített identitással.
| Hitelesítés | Nem található | Az eszköz/modul található. | Hitelesítési konfiguráció ellenőrzése – győződjön meg arról, hogy az állomásnév helyes, az eszköz létezik IoT Hubban, és rendelkezik egy azureiotsecurity Twin modullal. |  Hitelesítési konfiguráció ellenőrzése – győződjön meg arról, hogy az állomásnév helyes, az eszköz létezik IoT Hubban, és rendelkezik egy azureiotsecurity Twin modullal. |
| Hitelesítés | Hiányzó konfiguráció | Hiányzik egy konfiguráció a *Authentication. config* fájlban. A hibaüzenetnek meg kell jelennie, hogy melyik kulcs hiányzik. | Adja hozzá a hiányzó kulcsot a *LocalConfiguration. JSON* fájlhoz.| Adja hozzá a hiányzó kulcsot a *Authentication. config* fájlhoz a részletekért lásd a [c#-localconfig-Reference](azure-iot-security-local-configuration-csharp.md) című témakört. |
| Hitelesítés | Nem értelmezhető konfiguráció | A konfigurációs érték nem elemezhető. A hibaüzenetnek azt kell megadnia, hogy melyik kulcsot nem lehet elemezni. A konfigurációs érték nem elemezhető, mert vagy az érték nem a várt típusú, vagy az érték a megengedett tartományon kívül esik. |Javítsa ki a kulcs értékét a **LocalConfiguration. JSON** fájlban. |Javítsa ki a kulcs értékét a **Authentication. config** fájlban a séma egyeztetéséhez. a részletekért tekintse meg a [cs-localconfig-Reference](azure-iot-security-local-configuration-c.md) című témakört.|
|

## <a name="restart-the-agent"></a>Az ügynök újraindítása

1. A biztonsági ügynök hibájának megkeresése és javítása után indítsa újra az ügynököt a következő parancs futtatásával:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Ha szükséges, ismételje meg az előző folyamatokat, hogy kényszerítse az ügynököt, és kérje le a hibákat, ha az ügynök továbbra is elmulasztja az indítási folyamatot.

## <a name="next-steps"></a>További lépések

- A IoT-szolgáltatás [áttekintésének](overview.md) Azure Security Center olvasása
- További információ a IoT- [architektúra](architecture.md) Azure Security Center
- A IoT [szolgáltatás](quickstart-onboard-iot-hub.md) Azure Security Centerának engedélyezése
- A IoT szolgáltatással kapcsolatos [Gyakori kérdések](resources-frequently-asked-questions.md) Azure Security Center beolvasása
- Ismerje meg, hogyan érheti el a [nyers biztonsági információkat](how-to-security-data-access.md)
- [Javaslatok](concept-recommendations.md) ismertetése
- Biztonsági [riasztások](concept-security-alerts.md) ismertetése
