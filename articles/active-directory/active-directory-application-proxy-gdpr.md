---
title: Az Azure Active Directory alkalmazásproxyjával GDPR |} Microsoft Docs
description: További tudnivalók GDPR az Azure Active Directory alkalmazásproxyjával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a3df15743b4918d72fac5f8769a2d3ee721a452f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>Az Azure Active Directory alkalmazásproxyjával GDPR  

Azure Active Directory (Azure AD) alkalmazásproxy más Microsoft-szolgáltatások és funkciók együtt GDPR-kompatibilis. A Microsoft GDPR támogatásával kapcsolatos további tudnivalókért lásd: [licencelési feltételeit és dokumentáció](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Ez a funkció tartalmazza az összekötők a számítógépeken, mert nincsenek néhány eseményeket, amelyekre szüksége van ahhoz, hogy megfelelő GDPR figyelése. Alkalmazásproxy hoz létre a következő napló típusú, amely tartalmazhat EUII:

- Összekötő eseménynaplók
- Windows-eseménynaplók,

A megfelelő GDPR időtartamú két módja van:

- Törölje és kérelmek exportálása felmerülő

- Kapcsolja ki a naplózás

Esetén:

- A Windows eseménynaplóiban keresse meg, az adatmegőrzés konfigurálásához tekintse [Eseménynaplók beállításai](https://technet.microsoft.com/library/cc952132.aspx). 
- A Windows eseménynaplóban, általános információkat lásd: [használata a Windows Eseménynapló](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).


A csatlakozás esemény mindaddig naplózásra kerül található `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. A következő szakaszokban azt a kapcsolódó lépések a connector eseménynaplók. Összes csatlakozó számítógépeken a lépések elvégzése után.
 

## <a name="processing-requests"></a>Kérelmek feldolgozása

A kérelmek áll azokért a három különböző típusa van: 

- Törlés
- Nézet
- Exportálás
 
Nézet feldolgozni / kérelmek exportálása, nyissa meg a naplófájlok bejegyzéseket kereséséhez végig kell. 

Mivel a naplók szövegfájlok, úgy keresheti meg, például használatával a `findstr` parancs beírásával a felhasználóhoz kapcsolódó bejegyzéseket. Mivel előfordulhat, hogy ezek a naplók keresése a következő mezőket: 

- Felhasználói azonosító
- A Kerberos által korlátozott delegálást használó alkalmazások konfigurált felhasználónév típusa:
    - Egyszerű felhasználónév.
    - Helyszíni felhasználó egyszerű neve
    - Egyszerű felhasználónév felhasználónév része
    - Helyszíni felhasználó egyszerű nevének felhasználónév része
    - Helyszíni SAM-fiók neve 

 
A mezők gyűjtése, majd a felhasználó való megosztására.
Törlési kérelmek feldolgozásához, kell törölnie a megfelelő naplókat. Újraindíthatja az összekötő szolgáltatást (Microsoft Azure AD alkalmazásproxy-összekötő) létrehozni egy új naplófájl. Az új naplófájl lehetővé teszi, hogy törölje a régi naplófájlokat. Kövesse a folyamatot nézet / exportálása az összes releváns napló keresése, és szelektív módon törli ezeket a mezőket vagy fájlokat. Még mindig csak törölheti a régi naplófájlokat, ha nincs többé szükség.


## <a name="turn-off-connector-logs"></a>Kapcsolja ki a connector naplófájljai

Connector naplófájljaiban kikapcsolásához állítsa be kell `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` eltávolítja a kijelölt sor: 


![Konfiguráció](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>További lépések

Az Azure AD-alkalmazásproxy áttekintését lásd: [hogyan biztosíthat biztonságos távoli hozzáférést a helyszíni alkalmazások](manage-apps/application-proxy.md).

