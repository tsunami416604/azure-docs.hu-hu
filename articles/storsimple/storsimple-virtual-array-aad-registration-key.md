---
title: A StorSimple virtuális tömbök új hitelesítési |} Microsoft Docs
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
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: e33a3f843017ec24f3a79701fac9a62e15b4f9ba
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109188"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>A StorSimple az új hitelesítés használatára

## <a name="overview"></a>Áttekintés

A StorSimple Device Manager szolgáltatás a Microsoft Azure-ban és több StorSimple virtuális tömbök csatlakozik. Naprakész, StorSimple Device Manager szolgáltatás egy hozzáférés-vezérlés szolgáltatás (ACS) használta a szolgáltatást, hogy a StorSimple eszköz hitelesítéséhez. Az ACS-mechanizmus a rendszer hamarosan elavult, majd egy Azure Active Directory (AAD) hitelesítés helyett.

Ebben a cikkben szereplő információk csak mindkét StorSimple 1200 adatsorozat virtuális tömbök csak alkalmazható. Ez a cikk ismerteti az AAD-hitelesítés és a kapcsolódó új Szolgáltatásregisztrációs kulcs és a StorSimple-eszközökre vonatkozik, a tűzfalszabályok módosításai részleteit.

Az AAD-hitelesítés történik a StorSimple virtuális tömbök (1200-as modell) 1 vagy újabb verziót futtató a frissítés.

Az AAD-hitelesítés bevezetése miatt bekövetkező változások:

- A tűzfalszabályok URL-mintával.
- Szolgáltatásregisztrációs kulcs.

Ezeket a változásokat a következő szakaszokban részletesen ismertetése.

## <a name="url-changes-for-aad-authentication"></a>URL-cím módosításainak AAD-hitelesítés

Győződjön meg arról, hogy a szolgáltatás az AAD-alapú hitelesítést használ, a minden felhasználó tartalmaznia kell az új hitelesítési URL-címek be a tűzfalszabályok között.

StorSimple virtuális tömb használata esetén győződjön meg arról, hogy a következő URL-cím szerepel-e a tűzfalszabályok:

| Az URL-minta                         | Felhő | Összetevő/funkció         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |Az AAD-hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Az USA kormányzata |Az AAD-hitelesítési szolgáltatás      |

Az URL-cím teljes listáját a StorSimple virtuális tömbök mintákra, Ugrás [URL-mintával tűzfalszabályaira vonatkozó](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Ha a hitelesítés URL-címe nem szerepel a tűzfalszabályok túl a megszűnésének dátumát, a felhasználók jelenik meg a kritikus riasztás, amely a StorSimple-eszköz nem tudta hitelesíteni a szolgáltatással. A szolgáltatás nem lesz képes kommunikálni az eszközt. Ha a felhasználók ezt a figyelmeztetést, az új hitelesítési URL-cím szükséges. A figyelmeztetéssel kapcsolatos további információkért látogasson el [riasztások segítségével figyelheti a StorSimple eszköz](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Eszköz verziója és a hitelesítés módosítása

A StorSimple virtuális tömb használata esetén a következő táblázat segítségével határozza meg, mit kell tennie, futtató eszköz szoftver verziója alapján.

| Ha az eszköz fut.  | A következő művelet elvégzése                                    |
|----------------------------|--------------------------------------------------------------|
| Frissítés 1.0-s vagy újabb és offline állapotban. <br> Megjelenik egy figyelmeztetés, győződjön meg arról, hogy az URL-címe nem szerepel az engedélyezési listán.| 1. A tűzfalszabályok a hitelesítési URL-cím módosításához. Lásd: [hitelesítési URL-címek](#url-changes-for-aad-authentication). <br> 2. [Az AAD-regisztrációs kulcs beolvasása a szolgáltatástól](#aad-based-registration-keys). <br> 3. 1-5 lépések végrehajtásával [csatlakozás a Windows PowerShell felületen, a virtuális tömb](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Használjon `Invoke-HcsReRegister` parancsmag futtatásával regisztrálja az eszközt a Windows PowerShell segítségével. Adja meg az előző lépésben kapott kulcsot.|
| 1.0-s vagy újabb frissítés és az eszköz online állapotban.| Nincs szükség semmilyen műveletre.                                       |
| Frissítés 0,6 vagy korábbi és az eszköz offline állapotban. | 1. [Töltse le az 1.0-s frissítési katalógus kiszolgálón keresztül](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Frissítés 1.0 alkalmazza a helyi webes felhasználói felületen](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Az AAD-regisztrációs kulcs beolvasása a szolgáltatástól](#aad-based-registration-keys). <br>4. 1-5 lépések végrehajtásával [csatlakozás a Windows PowerShell felületen, a virtuális tömb](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Használjon `Invoke-HcsReRegister` parancsmag futtatásával regisztrálja az eszközt a Windows PowerShell segítségével. Adja meg az előző lépésben kapott kulcsot.|
| Frissítés 0,6 vagy korábbi és az eszköz online állapotban | A tűzfalszabályok a hitelesítési URL-cím módosításához.<br> Frissítés 1.0 telepítse az Azure portálon keresztül. |

## <a name="aad-based-registration-keys"></a>Az AAD-alapú regisztrációs kulcsokat

A StorSimple virtuális tömbök, új kulcsot használnak az AAD-alapú regisztrációs megkezdi a 1.0-s frissítésében. A regisztrációs kulcsokat segítségével regisztrálja az eszközt a StorSimple eszköz Manager szolgáltatás.

Az új AAD szolgáltatás regisztrációs kulcsokat 0,6 vagy régebbi verziót futtató a frissítés egy StorSimple virtuális tömbök használata nem használható. A szolgáltatás regisztrációs kulcsának újragenerálása kell. Újragenerálja a kulcsot, ha az új kulccsal a következő eszközök regisztrálásához. A régi kulcsa már nem érvényes.

- Az aad-ben új regisztrációs kulcsot 3 nap után lejár.
- Az aad-ben regisztrációs kulcsok használata csak virtuális StorSimple 1200 sorozat tömbállandó futó Update 1 vagy újabb verziót. Az aad-ben kulcsot az a StorSimple 8000 series eszköz nem fog működni.
- Az aad-ben regisztrációs kulcs hosszabb, mint a megfelelő ACS-regisztrációs kulcsokat.

A következő lépésekkel hozzon létre egy AAD szolgáltatás regisztrációs kulcsot.

#### <a name="to-generate-the-aad-service-registration-key"></a>Az AAD szolgáltatás regisztrációs kulcs létrehozása

1. A **StorSimple Device Manager**, és **felügyeleti &gt;**  **kulcsok**.
    
    ![Ugrás a kulcsok](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kattintson a **kulcs**.

    ![Kattintson a újragenerálása](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Az új kulcs másolása. A régebbi kulcs már nem működik.

    ![Regenerate megerősítése](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>További lépések

* További információ a központi telepítése [StorSimple virtuális tömb](storsimple-virtual-array-deploy1-portal-prep.md)
