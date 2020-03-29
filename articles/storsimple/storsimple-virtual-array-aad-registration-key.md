---
title: Új hitelesítés a StorSimple virtuális tömbökhöz
description: A cikk bemutatja, hogyan használhatja az AAD-alapú hitelesítést a szolgáltatáshoz, hogyan hozhat létre új regisztrációs kulcsot, és hogyan végezheti el az eszközök manuális regisztrációját.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 89f367e866c1a794f4359c76b8b8a8a9cfefd50d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273806"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Az új hitelesítés használata a StorSimple

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

A StorSimple Eszközkezelő szolgáltatás a Microsoft Azure-ban fut, és több StorSimple virtuális tömbhöz csatlakozik. A StorSimple Eszközkezelő szolgáltatás a mai napig egy hozzáférés-vezérlési szolgáltatást (ACS) használt a szolgáltatás StorSimple-eszközre való hitelesítéséhez. Az ACS-mechanizmus hamarosan elavulttá válik, és egy Azure Active Directory (AAD) hitelesítésváltja fel.

Az ebben a cikkben található információk csak a StorSimple 1200 sorozatú virtuális tömbökre vonatkoznak. Ez a cikk ismerteti az AAD-hitelesítés és a kapcsolódó új szolgáltatás regisztrációs kulcs és a tűzfalszabályok módosításai a StorSimple-eszközökre vonatkozó részleteit.

Az AAD-hitelesítés az 1.

Az AAD-hitelesítés bevezetése miatt változások történnek a következőkben:

- A tűzfalszabályok URL-mintái.
- Szolgáltatás regisztrációs kulcsa.

Ezeket a változásokat részletesen tárgyaljuk a következő szakaszokban.

## <a name="url-changes-for-aad-authentication"></a>Az AAD-hitelesítés URL-módosításai

Annak érdekében, hogy a szolgáltatás AAD-alapú hitelesítést használjon, minden felhasználónak tartalmaznia kell az új hitelesítési URL-címeket a tűzfalszabályokban.

StorSimple virtuális tömb használata esetén győződjön meg arról, hogy a tűzfalszabályok a következő URL-címet tartalmazzák:

| URL-minta                         | Felhő | Összetevő/funkcionalitás         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |AAD hitelesítési szolgáltatás      |
| `https://login.microsoftonline.us` | Egyesült Államok – Államigazgatás |AAD hitelesítési szolgáltatás      |

A StorSimple virtuális tömbök URL-mintáinak teljes listáját a [tűzfalszabályok URL-mintáinak](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules)mentik meg.

Ha a hitelesítési URL nem szerepel a tűzfalszabályokban az eprecációs dátumon túl, a felhasználók egy kritikus riasztást látnak, amely szerint a StorSimple-eszköznem tudta hitelesíteni a szolgáltatást. A szolgáltatás nem tud kommunikálni az eszközzel. Ha a felhasználók látják ezt a riasztást, meg kell adniuk az új hitelesítési URL-címet. A riasztásról további információt a [StorSimple-eszköz figyeléséhez riasztások használata című](storsimple-virtual-array-manage-alerts.md#networking-alerts)területen talál.

## <a name="device-version-and-authentication-changes"></a>Az eszköz verzió- és hitelesítési módosításai

Ha StorSimple virtuális tömböt használ, az alábbi táblázat segítségével határozza meg, hogy milyen műveletet kell végrehajtania a futtatott eszközszoftver-verzió alapján.

| Ha az eszköz fut  | A következő művelet végrehajtása                                    |
|----------------------------|--------------------------------------------------------------|
| Frissítse az 1.0-s vagy újabb verziót, és offline állapotban van. <br> Megjelenik egy figyelmeztetés, hogy az URL-cím nem szerepel az engedélyezési listán.| 1. Módosítsa a tűzfalszabályokat úgy, hogy az tartalmazza a hitelesítési URL-címet. Lásd a [hitelesítési URL-címeket.](#url-changes-for-aad-authentication) <br> 2. [Szerezd meg az AAD regisztrációs kulcsot a szolgáltatásból.](#aad-based-registration-keys) <br> 3. Hajtsa végre [Connect to the Windows PowerShell interface of the virtual array](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)az 1-5.<br> 4. `Invoke-HcsReRegister` A parancsmag segítségével regisztrálja az eszközt a Windows PowerShell en keresztül. Adja meg az előző lépésben kapott kulcsot.|
| Frissítse az 1.0-s vagy újabb verziót, és az eszköz online állapotban van.| Semmit nem kell tenni.                                       |
| Frissítse a 0.6-os vagy korábbi frissítést, és az eszköz offline állapotban van. | 1. [Töltse le az 1.0-s frissítést katalóguskiszolgálón keresztül.](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix)<br>2. [Alkalmazza a 1.0-s frissítést a helyi webes felhasználói felületen keresztül.](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix)<br>3. [Szerezd meg az AAD regisztrációs kulcsot a szolgáltatásból.](#aad-based-registration-keys) <br>4. Hajtsa végre [Connect to the Windows PowerShell interface of the virtual array](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)az 1-5.<br>5. `Invoke-HcsReRegister` A parancsmag segítségével regisztrálja az eszközt a Windows PowerShell en keresztül. Adja meg az előző lépésben kapott kulcsot.|
| 0.6-os vagy korábbi frissítés, és a készülék online állapotban van | Módosítsa a tűzfalszabályokat úgy, hogy az tartalmazza a hitelesítési URL-címet.<br> Telepítse az 1.0-s frissítést az Azure Portalon keresztül. |

## <a name="aad-based-registration-keys"></a>AAD-alapú regisztrációs kulcsok

A StorSimple virtuális tömbök 1.0-s frissítésének kezdő frissítése új AAD-alapú regisztrációs kulcsokat használ. A regisztrációs kulcsok segítségével regisztrálja a StorSimple Eszközkezelő szolgáltatást az eszközhöz.

Az új AAD szolgáltatás regisztrációs kulcsai nem használhatók, ha 0.6-os vagy korábbi frissítést futtató StorSimple virtuális tömböket használ. Újra kell generálnia a szolgáltatás regisztrációs kulcsát. A kulcs újragenerálása után az új kulcs az összes további eszköz regisztrálására szolgál. A régi kulcs már nem érvényes.

- Az új AAD regisztrációs kulcs 3 nap után lejár.
- Az AAD regisztrációs kulcsok csak az 1. A StorSimple 8000 sorozatú eszköz AAD regisztrációs kulcsa nem fog működni.
- Az AAD regisztrációs kulcsok hosszabbak, mint a megfelelő ACS regisztrációs kulcsok.

Hajtsa végre az alábbi lépéseket az AAD-szolgáltatás regisztrációs kulcsának létrehozásához.

#### <a name="to-generate-the-aad-service-registration-key"></a>Az AAD szolgáltatás regisztrációs kulcsának létrehozása

1. A **StorSimple Eszközkezelőben**nyissa meg a **Felügyeleti &gt; ** **kulcsok lehetőséget.**
    
    ![Ugrás a kulcsokra](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Kattintson **a Kulcs létrehozása gombra.**

    ![Kattintson az újragenerálás gombra](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Másolja az új kulcsot. A régebbi kulcs már nem működik.

    ![Regenerálás megerősítése](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>További lépések

* További információ a [StorSimple virtuális tömb telepítéséről](storsimple-virtual-array-deploy1-portal-prep.md)
