---
title: A csoportok – Azure Active Directory dinamikus csoporttagság problémák javítása |} A Microsoft Docs
description: Hibaelhárítási tippek az Azure AD dinamikus csoporttagság.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eededcc180d7652fd52c79b85ca3c34f65a22a4
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791552"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Hibaelhárításához és megoldásához kapcsolatos problémák szerepelnek

## <a name="troubleshooting-group-creation-issues"></a>Csoport létrehozásával kapcsolatos problémák elhárítása

**Tudok biztonsági csoport létrehozása az Azure Portalon le van tiltva, de a csoportok továbbra is PowerShell-lel hozható létre** a **felhasználók létrehozhatnak biztonsági csoportokat az Azure-portálokon** beállítása az Azure portál-vezérlőelemek az-e a nem rendszergazda felhasználók létrehozhatnak biztonsági csoportokat a hozzáférési panelen és az Azure Portalon. Nem szabályozza a biztonsági csoport létrehozása Powershell segítségével.

Csoport létrehozása a Powershell nem rendszergazdai felhasználók letiltása:
1. Győződjön meg arról, hogy a nem rendszergazda felhasználók hozhatnak létre csoportokat:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Ha a visszaadott érték `UsersPermissionToCreateGroupsEnabled : True`, akkor a nem rendszergazda jogosultságú felhasználók létrehozhatnak csoportokat. Ez a funkció letiltása:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**A maximális csoportok hiba engedélyezett, ha a Powershell egy dinamikus csoport létrehozása során kapott**<br/>
Ha egy üzenetet kap a Powershell jelző _max engedélyezett csoportok száma elérte a dinamikus csoportházirendek_, ez azt jelenti, hogy elérte a maximális korlát dinamikus csoportok a bérlőben. Bérlőnként dinamikus csoportok maximális száma érték az 5 000.

Minden olyan új dinamikus csoportok létrehozására, először szüksége néhány meglévő dinamikus csoportok törlése. Nincs lehetőség a korlát növelését.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Dinamikus csoporttagságok hibaelhárítása

**Konfiguráltam egy szabály a csoport, de a csoport frissítésére nincs tagságok**<br/>
1. Ellenőrizze a felhasználó vagy a szabály lévő értékeket. Győződjön meg arról, vannak olyan felhasználók, amelyek megfelelnek a szabályt. Eszközökhöz ellenőrizze az eszköztulajdonságok annak érdekében, hogy minden szinkronizált attribútumok a várt értékeket tartalmazza.<br/>
2. Ellenőrizze, győződjön meg arról, ha befejeződött a feldolgozási állapot a tagságát. Ellenőrizheti a [tagság a feldolgozási állapot](groups-create-rule.md#check-processing-status-for-a-rule) és az utolsó frissítés dátuma a **áttekintése** a csoporthoz tartozó oldal.

Ha mindent rendben talál, engedélyezze a csoport feltöltése egy kis ideig. Az első feltöltésnél és szabálymódosítás után a bérlő méretétől függően ez akár 24 órát is jelenthet.

**Egy szabály konfiguráltam, de mostantól a meglévő csoporttagok, a szabály el lesznek távolítva**<br/>Ez az elvárt működés. A csoport tagjai meglévő el lesznek távolítva, amikor egy szabály engedélyezve van, vagy módosítani. A kiértékelés a szabály által visszaadott felhasználók tagjai a csoportba kerülnek.

**Nem látom a tagsága instantly hozzáadása vagy egy szabály, miért nem módosítása esetén?**<br/>Dedikált gyűjteménytagság értékelése egy aszinkron háttérfolyamat rendszeres időközönként történik. Mennyi ideig tart határozza meg a címtárban található felhasználók száma és mérete miatt a szabály létrehozta a csoportot. A felhasználók kis számú könyvtárak általában megjelenik a csoporttagsági változások kisebb, mint néhány perc múlva. A felhasználók nagy számú könyvtárak 30 percet is igénybe vehet, vagy hosszabb adatokkal való feltöltéséhez.

**Hogyan kényszerítheti a csoport már feldolgozásra?**<br/>
Jelenleg nincs semmilyen módon nem lehet automatikusan aktiválja a csoport igény szerint lesz feldolgozva. Azonban manuálisan is aktiválhatja az újrafeldolgozás a tagsági szabály hozzáadása egy szóköz végén frissítésével.  

**Egy szabály feldolgozási hiba jelent meg.**<br/>A következő táblázat felsorolja a dinamikus tagsági szabály előforduló gyakori hibák és a rendszernaplóban őket.

| Hiba történt a szabály elemző | Hiba használat | Javított használat |
| --- | --- | --- |
| Hiba: Az attribútum nem támogatott. |(user.invalidProperty -eq "Value") |(user.department - eq "value")<br/><br/>Ellenőrizze, hogy a rendszer az attribútum a [támogatott tulajdonságok listája](groups-dynamic-membership.md#supported-properties). |
| Hiba: Operátor nem támogatott az attribútum. |(user.accountEnabled – igaz tartalmaz) |(user.accountEnabled -eq true)<br/><br/>Az operátor nem támogatott a tulajdonság típusa (ebben a példában-tartalmaz nem használható írja be a logikai érték). Használja a megfelelő operátorok a tulajdonság típusát. |
| Hiba: Sémafordítási hiba történt a lekérdezés. | 1. (user.department - eq "Értékesítés") (user.department - eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. Hiányzó operátor. Használja a - és vagy - vagy két predikátumok csatlakoztatása<br>(user.department - eq "Értékesítés") – vagy (user.department - eq "Marketing")<br>2. Hiba – a használt reguláris kifejezést az felel meg<br>(user.userPrincipalName -match ".*@domain.ext")<br>vagy másik lehetőségként: (user.userPrincipalName-egyezik "@domain.ext$") |

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)