---
title: A dinamikus csoporttagságok problémáinak elhárítása – Azure AD | Microsoft Docs
description: Hibaelhárítási tippek a dinamikus csoporttagság Azure Active Directory
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74026544"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>A csoportok problémáinak elhárítása és megoldása

## <a name="troubleshooting-group-creation-issues"></a>Csoportos létrehozással kapcsolatos problémák elhárítása

**Letiltottam a biztonsági csoport létrehozását a Azure Portalban, de a csoportok továbbra** is létrehozhatók a PowerShell használatával A **felhasználók létrehozhatnak biztonsági csoportokat az Azure-portálok** beállításában a Azure Portal meghatározza, hogy a nem rendszergazda felhasználók hozhatnak-e létre biztonsági csoportokat a hozzáférési panelen vagy a Azure Portalban. Nem szabályozza a biztonsági csoportok létrehozását a PowerShell használatával.

A nem rendszergazda felhasználók csoport létrehozásának letiltása a PowerShellben:
1. Győződjön meg arról, hogy a nem rendszergazda felhasználók számára engedélyezett a csoportok létrehozása:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Ha visszatér `UsersPermissionToCreateGroupsEnabled : True`, akkor a nem rendszergazda felhasználók létrehozhatnak csoportokat. A funkció letiltása:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Egy dinamikus csoport a PowerShellben való létrehozásakor engedélyezett maximális csoportok hibaüzenetet kaptam**<br/>
Ha egy üzenet jelenik meg a PowerShellben, amely jelzi, hogy a _dinamikus csoportházirendek maximálisan engedélyezett csoportok száma elérte a megengedett értéket_, ez azt jelenti, hogy elérte a dinamikus csoportok maximális korlátját a bérlőben. A dinamikus csoportok maximális száma egy bérlőn 5 000.

Új dinamikus csoportok létrehozásához először törölnie kell néhány meglévő dinamikus csoportot. Nincs lehetőség a korlát növelésére.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Dinamikus csoporttagságok hibaelhárítása

**Konfiguráltam egy szabályt egy csoporton, de nem frissültek tagságok a csoportban**<br/>
1. Ellenőrizze a szabályban a felhasználó vagy az eszköz attribútumainak értékeit. Ellenőrizze, hogy vannak-e olyan felhasználók, akik megfelelnek a szabálynak. Eszközök esetében ellenőrizze az eszköz tulajdonságait, hogy minden szinkronizált attribútum tartalmazza-e a várt értékeket.<br/>
2. Ellenőrizze, hogy befejeződött-e a tagság feldolgozási állapota. A csoport **Áttekintés** lapján megtekintheti a [tagság feldolgozási állapotát](groups-create-rule.md#check-processing-status-for-a-rule) és a legutóbbi frissítés dátumát.

Ha minden jól látható, hagyjon némi időt a csoport feltöltésére. Az első feltöltésnél és szabálymódosítás után a bérlő méretétől függően ez akár 24 órát is jelenthet.

**Konfiguráltam egy szabályt, de mostantól a szabály meglévő tagjai törlődnek.**<br/>Ez az elvárt működés. A csoport meglévő tagjai el lesznek távolítva, amikor egy szabály engedélyezve van vagy megváltozott. A szabály kiértékelésével visszaadott felhasználók tagként lesznek hozzáadva a csoporthoz.

**Nem látom azonnal a tagsági változásokat, amikor hozzáadok vagy módosítok egy szabályt, miért nem?**<br/>A dedikált tagság kiértékelése rendszeres időközönként, aszinkron háttérbeli folyamaton történik. A folyamat hosszának meghatározása a címtárban lévő felhasználók száma és a szabály eredményeképpen létrehozott csoport mérete alapján történik. A kis számú felhasználót tartalmazó könyvtárak általában kevesebb, mint néhány perc alatt megjelennek a csoporttagság változásai. A sok felhasználóval rendelkező címtárak akár 30 percet vagy hosszabb időt is igénybe vehetnek.

**Hogyan lehet kényszeríteni a csoport feldolgozását most?**<br/>
Jelenleg nem lehet automatikusan elindítani a csoportot a feldolgozás igény szerint. Az újrafeldolgozást azonban manuálisan is aktiválhatja úgy, hogy a tagsági szabályt úgy frissíti, hogy a végén szóközt adjon hozzá.  

**Szabály-feldolgozási hiba történt**<br/>Az alábbi táblázat a dinamikus tagsági szabályok gyakori hibáit és azok javítását ismerteti.

| Szabály-elemzési hiba | Hiba használata | Javított használat |
| --- | --- | --- |
| Hiba: az attribútum nem támogatott. |(User. invalidProperty-EQ "value") |(User. Department-EQ "value")<br/><br/>Győződjön meg arról, hogy az attribútum a [támogatott tulajdonságok listáján](groups-dynamic-membership.md#supported-properties)található. |
| Hiba: az operátor nem támogatott az attribútumon. |(User. accountEnabled – igaz értéket tartalmaz) |(User. accountEnabled – EQ true)<br/><br/>A használt operátor nem támogatott a tulajdonság típusaként (ebben a példában a-tartalmazza a logikai típus nem használható). A tulajdonság típusához használja a megfelelő operátorokat. |
| Hiba: a lekérdezés fordítási hibája. | 1. (felhasználó. részleg – EQ "értékesítés") (felhasználó. részleg – EQ "marketing")<br>2. (User. userPrincipalName-Match "*@domain.ext") | 1. hiányzó operátor. A és a vagy a két illesztési predikátum használata<br>(felhasználó. részleg – EQ "értékesítés") – vagy (User. Department-EQ "marketing")<br>2. hiba a-egyezéssel használt reguláris kifejezésben<br>(User. userPrincipalName-Match ". *@domain.ext")<br>vagy másik lehetőségként: (User. userPrincipalName-@domain.extMatch "$") |

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)