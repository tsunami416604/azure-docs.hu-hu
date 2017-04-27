---
title: "Az Outlook használata az Azure RemoteAppban | Microsoft Docs"
description: "Ismerje meg, hogyan konfigurálhatja és használhatja az Outlookot az Azure RemoteAppban | Microsoft Azure"
services: remoteapp
documentationcenter: 
author: pavithir
manager: mbaldwin
ms.assetid: cb2a498f-9539-4522-a874-542114926a61
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a6d4fbdf0e552f50673092183e893841ec0c5aa4
ms.lasthandoff: 03/31/2017


---
# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>A Microsoft Outlook használata az Azure RemoteAppban
> [!IMPORTANT]
> Az Azure RemoteApp 2017. augusztus 31-ét követően megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Az Azure RemoteApp támogatja a Microsoft Outlook O365 használatát. További információk az [Office működéséről az Azure RemoteAppban](remoteapp-officesubscription.md). Az Outlook az Azure RemoteAppban való használatához van néhány ajánlott beállítás.

## <a name="cached-mode"></a>Gyorsítótáras üzemmód
A gyorsítótáras üzemmód a javasolt konfiguráció, ha az Outlookot az Azure RemoteAppban használja. Ha úgy konfigurál egy Outlook 2013-fiókot, hogy a gyorsítótáras Exchange üzemmódot használja, az Outlook 2013 a felhasználó Microsoft Exchange-postaládájának helyi másolatából működik, amelyet a rendszer egy offline adatfájlban (OST-fájl) tárol a felhasználó számítógépén az offline címjegyzékkel (OAB) együtt. A gyorsítótárazott postaláda és OAB rendszeresen frissül az O365 szolgáltatásból. További információk a [gyorsítótáras és az online üzemmód közötti különbségekről](https://technet.microsoft.com/library/jj683103.aspx).

A felhasználók a fiók beállításakor választhatják ki a **gyorsítótáras Exchange üzemmódot** vagy az **offline üzemmódot**, illetve módosíthatják a fiók beállításait. Emellett az Office testreszabási eszköz (OCT) vagy csoportházirend segítségével elvégezhető az egyik vagy másik üzemmód központi telepítése.  

Olvassa el [a gyorsítótáras üzemmód engedélyezésének lépésenkénti útmutatóját](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Keresés
Az Azure RemoteAppban az Outlookon belüli keresésekre korlátozások vonatkoznak. Az Azure RemoteApp készletbe vont virtuális gépekkel teszi lehetővé a felhasználói munkameneteket. A keresések indexelése a gép azonosítójától függ, amely különbözik a különböző virtuális gépek esetében. Lehetséges, hogy minden alkalommal, amikor egy felhasználó bejelentkezik az Azure RemoteAppba, a rendszer egy új virtuális géphez irányítja át. Ez azt jelenti, hogy ha engedélyezzük a helyi keresést, az indexelő minden alkalommal fut, amikor megváltozik a gép azonosítója (amikor a felhasználó eltérő virtuális gépet használ). Az .OST fájl méretétől függően előfordulhat, hogy az indexelő sok időt igényel a befejezéshez, és elhasználja a más alkalmazásokhoz szükséges erőforrásokat. A keresés ilyenkor nemcsak lassú, de előfordulhat, hogy eredményeket sem ad. Erre megoldást jelenthet egy online módú fiókprofil, azonban ettől csökkenne az általános teljesítmény a helyi gyorsítótár hiánya miatt (a gyorsítótárazott és az online mód közötti különbségekkel kapcsolatos további információkat a fenti hivatkozást követve tekintheti meg). Az indexelt/helyi keresés sajnos nem tiltható le, és az online keresés nem engedélyezhető alapértelmezés szerint az Outlook 2013-ban.


