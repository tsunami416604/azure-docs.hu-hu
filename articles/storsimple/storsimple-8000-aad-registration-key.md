---
title: Új hitelesítés használata a StorSimple 8000 Eszközkezelő szolgáltatás az Azure-ban |} Microsoft Docs
description: A szolgáltatás az AAD-alapú hitelesítést használ, hozzon létre új regisztrációs kulcsot, és az eszközök kézi regisztrálására ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: e6e792c31f9856bcaf1d777e534dcac8d8be3dd3
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113527"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>A StorSimple az új hitelesítés használatára

## <a name="overview"></a>Áttekintés

A StorSimple Device Manager szolgáltatás a Microsoft Azure-ban és több StorSimple eszközt csatlakozik. Naprakész, StorSimple Device Manager szolgáltatás egy hozzáférés-vezérlés szolgáltatás (ACS) használta a szolgáltatást, hogy a StorSimple eszköz hitelesítéséhez. Az ACS-mechanizmus a rendszer hamarosan elavult, majd egy Azure Active Directory (AAD) hitelesítés helyett. További információkért nyissa meg a következő közlemények ACS érvénytelenítése és AAD-hitelesítés használatát.

- [A jövőben is Azure ACS az Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [A Microsoft hozzáférés-vezérlési szolgáltatásban a jövőbeni változtatásokról](https://azure.microsoft.com/en-in/blog/acs-access-control-service-namespace-creation-restriction/)

Ez a cikk ismerteti az AAD-hitelesítés és a kapcsolódó új Szolgáltatásregisztrációs kulcs és a StorSimple-eszközökre vonatkozik, a tűzfalszabályok módosításai részleteit. Ebben a cikkben szereplő információk csak a StorSimple 8000 sorozat eszközeire érvényesek alkalmazható.

Az AAD-hitelesítés 5-ös vagy újabb verzióját futtató frissítési StorSimple 8000 sorozat eszköz történik. Az AAD-hitelesítés bevezetése miatt bekövetkező változások:

- A tűzfalszabályok URL-mintával.
- Szolgáltatásregisztrációs kulcs.

Ezeket a változásokat a következő szakaszokban részletesen ismertetése.

## <a name="url-changes-for-aad-authentication"></a>URL-cím módosításainak AAD-hitelesítés

Győződjön meg arról, hogy a szolgáltatás az AAD-alapú hitelesítést használ, a minden felhasználó tartalmaznia kell az új hitelesítési URL-címek be a tűzfalszabályok között.

A StorSimple 8000 series használata esetén győződjön meg arról, hogy a következő URL-cím szerepel-e a tűzfalszabályok:

| Az URL-minta                         | Felhő | Összetevő/funkció         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |Az AAD-hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Az USA kormányzata |Az AAD-hitelesítési szolgáltatás      |

Az URL-cím teljes listáját a StorSimple 8000 sorozat eszközeire mintákra, Ugrás [URL-mintával tűzfalszabályaira vonatkozó](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Ha a hitelesítés URL-címe nem szerepel a tűzfalszabályok túl a megszűnésének dátumát, és az eszköz az 5. frissítésétől futtatja, a felhasználók egy URL-cím riasztás tekintse meg. A felhasználók az új hitelesítési URL-címmel kell. Ha az eszköz 5 frissítés előtti verziót futtat, a felhasználók a szívverési riasztások jelenik meg. Minden esetben a StorSimple-eszköz nem tudja hitelesíteni a szolgáltatással, és a szolgáltatás nem tud kommunikálni az eszközt.

## <a name="device-version-and-authentication-changes"></a>Eszköz verziója és a hitelesítés módosítása

Ha a StorSimple 8000 series eszközt használ, a következő táblázat segítségével határozza meg, mit kell tennie, futtató eszköz szoftver verziója alapján.

| Ha az eszköz fut.| A következő művelet elvégzése                                    |
|--------------------------|------------------------|--------------------|--------------------------------------------------------------|
| 5-ös vagy újabb frissítés és az eszköz offline állapotban. <br> Látni egy riasztást, győződjön meg arról, hogy URL-címe nem szerepel az engedélyezési listán.|1. A tűzfalszabályok a hitelesítési URL-cím módosításához. Lásd: [hitelesítési URL-címek](#url-changes-for-aad-authentication).<br>2. [Az AAD-regisztrációs kulcs beolvasása a szolgáltatástól](#aad-based-registration-keys).<br>3. [A Windows PowerShell-felületet a StorSimple 8000 series eszköz csatlakozni](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Használjon `Redo-DeviceRegistration` parancsmag futtatásával regisztrálja az eszközt a Windows PowerShell segítségével. Adja meg az előző lépésben kapott kulcsot.|
| 5-ös vagy újabb frissítés és az eszköz online.| Nincs szükség semmilyen műveletre.                                       |
| 4-es vagy korábbi frissítési és az eszköz offline állapotban. |1. A tűzfalszabályok a hitelesítési URL-cím módosításához.<br>2. [Töltse le a frissítés 5 katalógus kiszolgálón keresztül](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [5. frissítésétől alkalmazni a gyorsjavítások a módszerrel](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Az AAD-regisztrációs kulcs beolvasása a szolgáltatástól](#aad-based-registration-keys).<br>5. [A Windows PowerShell-felületet a StorSimple 8000 series eszköz csatlakozni](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Használjon `Redo-DeviceRegistration` parancsmag futtatásával regisztrálja az eszközt a Windows PowerShell segítségével. Adja meg az előző lépésben kapott kulcsot.|
| 4-es vagy korábbi frissítési és az eszköz online állapotban. |A tűzfalszabályok a hitelesítési URL-cím módosításához.<br> 5. frissítésétől telepítse az Azure portálon keresztül.              |
| Gyári beállításokra történő visszaállításának frissítési 5 előtt verzióra.      |A portál egy AAD-alapú regisztrációs kulcsot jeleníti meg, az eszköz régebbi szoftver futása közben. Kövesse a fenti forgatókönyvben Ha az eszköz fut Update 4-es vagy korábbi.              |

## <a name="aad-based-registration-keys"></a>Az AAD-alapú regisztrációs kulcsokat

5. frissítésétől kezdve a StorSimple 8000 sorozat eszközeire, új AAD-alapú regisztrációs kulcsot használnak. A regisztrációs kulcsokat segítségével regisztrálja az eszközt a StorSimple eszköz Manager szolgáltatás.

Az új AAD szolgáltatás regisztrációs kulcsokat nem használható, ha egy Update 4-es vagy korábbi (tartalmazza a egy régebbi eszközhöz tartozó eszközjogkivonat most) futtatása a StorSimple 8000 series eszközt használ.
Ebben a forgatókönyvben a szolgáltatás regisztrációs kulcsának újragenerálása kell. Újragenerálja a kulcsot, ha az új kulccsal a következő eszközök regisztrálásához. A régi kulcsa már nem érvényes.

- Az aad-ben új regisztrációs kulcsot 3 nap után lejár.
- Az aad-ben regisztrációs kulcsokat, csak a StorSimple 8000 sorozat eszközeire futtató Update 5 vagy újabb.
- Az aad-ben regisztrációs kulcs hosszabb, mint a megfelelő ACS-regisztrációs kulcsokat.

A következő lépésekkel hozzon létre egy AAD szolgáltatás regisztrációs kulcsot.

#### <a name="to-generate-the-aad-service-registration-key"></a>Az AAD szolgáltatás regisztrációs kulcs létrehozása

1. A **StorSimple Device Manager**, és **felügyeleti &gt;**  **kulcsok**. Használhatja a keresési sávon kereséséhez _kulcsok_.
    
2. Kattintson a **kulcs**.

    ![Kattintson a újragenerálása](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Az új kulcs másolása. A régebbi kulcs már nem fog működni.

    ![Regenerate megerősítése](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > A StorSimple felhő készülék a StorSimple 8000 series eszköz regisztrálva a szolgáltatás létrehozásakor, nem regisztrációs kulcs létrehozása folyamatban van a létrehozása közben. Várjon, amíg befejeződik, és létrehozza a regisztrációs kulcs létrehozásához.

## <a name="next-steps"></a>További lépések

* További információ a központi telepítése [StorSimple 8000 series eszköz](storsimple-8000-deployment-walkthrough-u2.md).

