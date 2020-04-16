---
title: Többtényezős AAD-hitelesítés használata
description: A Synapse SQL támogatási kapcsolatai az SQL Server Management Studio (SSMS) rendszerből az Active Directory univerzális hitelesítés használatával.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 8e8f17f54f52aef6f552b7c211aa8f0e498b94df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424922"
---
# <a name="using-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Többtényezős AAD-hitelesítés használata szinapsze SQL-sel (SSMS-támogatás az MFA-hoz)

A Synapse SQL támogatási kapcsolatai az SQL Server Management Studio (SSMS) rendszerből az *Active Directory univerzális hitelesítés használatával.* 

Ez a cikk ismerteti a különböző hitelesítési beállítások közötti különbségeket, valamint az univerzális hitelesítés használatával kapcsolatos korlátozásokat. 

**Töltse le a legújabb SSMS** - Az ügyfélszámítógépen, töltse le a legújabb verzióját SSMS, [a Letöltés SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). 

**Töltse le a legújabb SSMS** - Az ügyfélszámítógépen, töltse le a legújabb verzióját SSMS, [a Letöltés SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

A cikkben tárgyalt összes funkcióhoz legalább 2017 júliusa, 17.2-es verzió használható.  A legutóbbi kapcsolat párbeszédpanelnek az alábbi képhez hasonlóan kell kinéznie:

![1mfa-univerzális csatlakozás](./media/mfa-authentication/1mfa-universal-connect.png "A Felhasználónév mező befejezése.")  

## <a name="the-five-authentication-options"></a>Az öt hitelesítési lehetőség  

Az Active Directory univerzális hitelesítés két nem interaktív hitelesítési módszert támogatja:
    - `Active Directory - Password`Hitelesítés
    - `Active Directory - Integrated`Hitelesítés

Két nem interaktív hitelesítési modell is létezik, amelyek számos különböző alkalmazásban használhatók (ADO.NET, JDCB, ODC stb.). Ez a két módszer soha nem eredményez előugró párbeszédpaneleket:

- `Active Directory - Password`
- `Active Directory - Integrated`

Az interaktív módszer az, amely támogatja az Azure többtényezős hitelesítés (MFA) is:

- `Active Directory - Universal with MFA`

Az Azure MFA segíti az adatok és alkalmazások védelmét az illetéktelen hozzáférésekkel szemben, miközben a felhasználói igényeknek megfelelő, egyszerű bejelentkezési folyamat használatát teszi lehetővé. Erős hitelesítést biztosít számos egyszerű ellenőrzési lehetőséggel (telefonhívás, szöveges üzenet, tűs intelligens kártyák vagy mobilalkalmazás-értesítés), amely lehetővé teszi a felhasználók számára, hogy kiválasszák az általuk preferált módszert. Interaktív MFA az Azure AD eredményezhet egy előugró párbeszédpanel érvényesítése.

A többtényezős hitelesítés leírását a Többtényezős hitelesítés című témakörben [tésszet.](../../active-directory/authentication//concept-mfa-howitworks.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD tartománynév vagy bérlőazonosító paraméter

Az [SSMS 17-es verziójától](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)kezdve a más Azure Active-könyvtárakból az aktuális Active Directoryba vendégfelhasználóként importált felhasználók megadhatják az Azure AD tartománynevet vagy a bérlői azonosítót, amikor csatlakoznak. 

Vendég felhasználók közé tartoznak a más Azure-AD-k, Microsoft-fiókok, például outlook.com, hotmail.com, live.com vagy más fiókok, például a gmail.com meghívott felhasználók. Ez az információ lehetővé teszi, hogy az **Active Directory univerzális, MFA-hitelesítéssel** azonosítsa a megfelelő hitelesítő jogosultságot. Ez a beállítás a Microsoft-fiókok (MSA) (outlook.com, hotmail.com, live.com vagy nem MSA-fiókok) támogatásához is szükséges. 

Az univerzális hitelesítéssel hitelesíteni kívánt összes felhasználónak meg kell adnia az Azure AD-tartománynevét vagy bérlői azonosítóját. Ez a paraméter az Aktuális Azure AD-tartománynevet/-bérlőazonosítót jelöli, amelyhez az Azure Server kapcsolódik. 

Ha például az Azure Server olyan `contosotest.onmicrosoft.com` Azure `joe@contosodev.onmicrosoft.com` AD-tartományhoz van társítva, `contosodev.onmicrosoft.com`ahol a felhasználó az Azure `contosotest.onmicrosoft.com`AD-tartományból importált felhasználóként van elrendezve, akkor a felhasználó hitelesítéséhez szükséges tartománynév a . 

Ha a felhasználó az Azure Serverhez kapcsolódó Azure AD natív felhasználója, és nem MSA-fiók, nincs szükség tartománynévre vagy bérlői azonosítóra. 

A paraméter beírásához (az SSMS 17.2-es verziójától kezdve) a Csatlakozás az **adatbázishoz** párbeszédpanelen töltse ki a párbeszédpanelt, válassza az **Active Directory – Univerzális MFA-hitelesítéssel** lehetőséget, kattintson a **Beállítások**gombra, töltse ki a **Felhasználónév párbeszédpanelt,** majd kattintson a **Kapcsolat tulajdonságai** fülre. 

Jelölje be az **AD tartománynév vagy a bérlőazonosító** jelölőnégyzetet, és adja meg a hitelesítési jogosultságot, például a tartománynevet (**contosotest.onmicrosoft.com**) vagy a bérlőazonosító GUID azonosítóját.  

   ![mfa-bérlő-ssms](./media/mfa-authentication/mfa-tenant-ssms.png)

Ha SSMS 18.x vagy újabb verziót futtat, akkor az AD tartománynév vagy a bérlői azonosító már nincs szükség a vendégfelhasználók számára, mert a 18.x vagy újabb verzió automatikusan felismeri azt.

   ![mfa-bérlő-ssms](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Azure AD üzleti támogatás   
Az Azure AD-felhasználók vendégfelhasználóként támogatott Azure AD B2B-forgatókönyvek (lásd: [Mi az Azure B2B együttműködés](../../active-directory/b2b/what-is-b2b.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) csatlakozhat a Synapse SQL csak a jelenlegi `CREATE USER` Azure AD-ben létrehozott és manuálisan leképezve egy adott adatbázistransact-SQL-nyilatkozattal. 

Például ha `steve@gmail.com` meghívást kap az `contosotest` Azure AD -be (az Azure Ad-tartománnyal), `contosotest.onmicrosoft.com`egy Azure AD-csoportot, például `usergroup` létre kell hozni az Azure AD-ben, amely tartalmazza a `steve@gmail.com` tagot. Ezután ezt a csoportot létre kell hozni egy adott adatbázishoz (azaz a MyDatabase-hez) az Azure `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` AD SQL-rendszergazda vagy az Azure AD DBO által egy Transact-SQL utasítás végrehajtásával. 

Az adatbázis-felhasználó létrehozása után `steve@gmail.com` a felhasználó `MyDatabase` bejelentkezhet az `Active Directory – Universal with MFA support`SSMS hitelesítési lehetőséghasználatával. 

A felhasználói csoport alapértelmezés szerint csak a csatlakozási engedéllyel és minden további adathozzáféréssel rendelkezik, amelyet a szokásos módon kell megadni. 

Ne feledje, hogy a felhasználónak `steve@gmail.com` vendégfelhasználóként be `contosotest.onmicrosoft.com` kell jelölnie a jelölőnégyzetet, és hozzá kell adnia az AD tartománynevet az SSMS-kapcsolat **tulajdonsága** párbeszédpanelen. Az **AD tartománynév vagy a bérlői azonosító** beállítás csak az MFA-kapcsolat beállításaival rendelkező univerzális beállítás esetén támogatott, ellenkező esetben a szürke lesz.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Univerzális hitelesítési korlátozások a Szinapszis SQL számára

- Az SSMS és az SqlPackage.exe az egyetlen olyan eszköz, amely jelenleg engedélyezve van az MFA számára az Active Directory univerzális hitelesítésen keresztül.
- SSMS 17.2-es verziója, támogatja a többfelhasználós egyidejű hozzáférést az univerzális hitelesítés sel az MFA-val. A 17.0-s és 17.1-es verzió egyetlen Azure Active Directory-fiókra korlátozta az SSMS univerzális hitelesítést használó példányának bejelentkezését. A bejelentkezéshez egy másik Azure AD-fiókként, az SSMS egy másik példányát kell használnia. (Ez a korlátozás az Active Directory univerzális hitelesítésre korlátozódik; különböző kiszolgálókra jelentkezhet be active directory jelszó-hitelesítéssel, Active Directory integrált hitelesítéssel vagy SQL Server-hitelesítéssel).
- Az SSMS támogatja az Active Directory univerzális hitelesítést az Objektumkezelő, a Lekérdezésszerkesztő és a Lekérdezéstároló megjelenítéshez.
- Az SSMS 17.2-es verziója a DacFx Wizard számára támogatja az adatok exportálását/kinyerését/telepítését. Miután egy adott felhasználó hitelesítése az univerzális hitelesítést használó kezdeti hitelesítési párbeszédpanelen keresztül történik, a DacFx varázsló ugyanúgy működik, mint az összes többi hitelesítési módszer esetében.
- Az SSMS táblatervező nem támogatja az univerzális hitelesítést.
- Az Active Directory univerzális hitelesítéshez nincsenek további szoftverkövetelmények, kivéve, hogy az SSMS támogatott verzióját kell használnia.  
- Az Active Directory hitelesítési könyvtár (ADAL) verziója az univerzális hitelesítéshez a legújabb ADAL.dll 3.13.9 elérhető verzióra frissült. Lásd: [Active Directory hitelesítési könyvtár 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>További lépések
További információt a Csatlakozás a [Synapse SQL-hez az SQL Server Management Studio cikkében](get-started-ssms.md) talál. 

