---
title: A dinamikus csoporttagságokkal kapcsolatos problémák megoldása - Azure AD | Microsoft dokumentumok
description: Hibaelhárítási tippek az Azure Active Directory dinamikus csoporttagságával kapcsolatban
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026544"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Csoportokproblémáinak elhárítása és megoldása

## <a name="troubleshooting-group-creation-issues"></a>Csoportlétrehozással kapcsolatos problémák elhárítása

**Letiltottam a biztonsági csoport létrehozását az Azure Portalon, de a powershellen keresztül továbbra is létrehozható csoportok** A **felhasználó biztonsági csoportokat hozhat létre** az Azure Portalok beállítás az Azure Portalon szabályozza, hogy a nem rendszergazdai felhasználók hozhatnak létre biztonsági csoportokat az Access panelen vagy az Azure Portalon. Nem szabályozza a biztonsági csoportok létrehozását a Powershellen keresztül.

A nem rendszergazda felhasználók csoportlétrehozásának letiltása a Powershellben:
1. Ellenőrizze, hogy a nem rendszergazda felhasználók hozhatnak-e létre csoportokat:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Ha visszaadja `UsersPermissionToCreateGroupsEnabled : True`a lehetőséget, akkor a nem rendszergazda felhasználók csoportokat hozhatnak létre. A szolgáltatás letiltása:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Kaptam egy max csoportok megengedett hiba, amikor megpróbál létrehozni egy dinamikus csoport Powershell**<br/>
Ha a Powershellben olyan üzenet jelenik meg, amely a _dinamikus csoportházirendek maximálisan engedélyezett csoportok számát_jelzi, ez azt jelenti, hogy elérte a bérlő dinamikus csoportjaira vonatkozó maximális korlátot. A dinamikus csoportok maximális száma bérlőnként 5000.

Új dinamikus csoportok létrehozásához először törölnie kell néhány meglévő dinamikus csoportot. Nem lehet megemelni a határt.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Dinamikus csoporttagságok hibaelhárítása

**Beállítottam egy szabályt egy csoportra, de a csoportban nem lett frissítés a tagság**<br/>
1. Ellenőrizze a szabályban lévő felhasználói vagy eszközattribútumok értékeit. Győződjön meg arról, hogy vannak olyan felhasználók, akik megfelelnek a szabálynak. Eszközök esetén ellenőrizze az eszköz tulajdonságait, hogy a szinkronizált attribútumok tartalmazzák-e a várt értékeket.<br/>
2. Ellenőrizze a tagság feldolgozási állapotát, és ellenőrizze, hogy befejeződött-e. A csoport **áttekintése** lapján ellenőrizheti a [tagság feldolgozási állapotát](groups-create-rule.md#check-processing-status-for-a-rule) és az utolsó frissített dátumot.

Ha minden jól néz ki, kérjük, hagyjon egy kis időt a csoport nak, hogy feltöltsék. Az első feltöltésnél és szabálymódosítás után a bérlő méretétől függően ez akár 24 órát is jelenthet.

**Beállítottam egy szabályt, de most a szabály meglévő tagjai törlődnek**<br/>Ez az elvárt működés. A csoport meglévő tagjai törlődnek, ha egy szabály engedélyezve van vagy módosul. A szabály kiértékeléséből visszaadott felhasználók a csoport tagjaként kerülnek hozzáadásra.

**Nem látom, hogy a tagság azonnal megváltozik, amikor hozzáadok vagy módosítok egy szabályt, miért ne?**<br/>A dedikált tagsági értékelés rendszeres időközönként, aszinkron háttérfolyamat tal történik. A folyamat élettartamát a címtárban lévő felhasználók száma és a szabály alapján létrehozott csoport mérete határozza meg. A kis számú felhasználóval rendelkező könyvtárak általában néhány percen belül láthatják a csoporttagság változásait. A nagy számú felhasználóval rendelkező könyvtárak feltöltése 30 percet vagy hosszabb időt is igénybe vehet.

**Hogyan kényszeríthetem a csoport feldolgozását?**<br/>
Jelenleg nincs mód arra, hogy automatikusan elindítja a csoportot igény szerint feldolgozandó. Az újrafeldolgozást azonban manuálisan is elindíthatja a tagsági szabály frissítésével, hogy a végén szóközt adjon hozzá.  

**Szabályfeldolgozási hibával találkoztam**<br/>Az alábbi táblázat a dinamikus tagsági szabályok gyakori hibáit és azok kijavítását sorolja fel.

| Szabályelemző hiba | Hibahasználat | Javított használat |
| --- | --- | --- |
| Hiba: Az attribútum nem támogatott. |(user.invalidProperty -eq "Érték") |(user.department -eq "érték")<br/><br/>Győződjön meg arról, hogy az attribútum szerepel a [támogatott tulajdonságok listáján.](groups-dynamic-membership.md#supported-properties) |
| Hiba: Az operátor nem támogatott attribútummal. |(user.accountEnabled -tartalmazza igaz) |(user.accountEnabled -eq igaz)<br/><br/>A használt operátor nem támogatott a tulajdonságtípushoz (ebben a példában a -contains nem használható logikai típuson). Használja a megfelelő operátorokat a tulajdonságtípushoz. |
| Hiba: Lekérdezésfordítási hiba. | 1. (user.department -eq "Értékesítés") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match@domain.ext"* ") | 1. Hiányzó operátor. -és -vagy -vagy két illesztési predikátum használata<br>(user.department -eq "Értékesítés") -vagy (user.department -eq "Marketing")<br>2. Hiba a reguláris kifejezésben, amelyet a -match<br>(user.userPrincipalName -match ".*@domain.ext")<br>vagy alternatívaként: (user.userPrincipalName@domain.ext-match " $") |

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)