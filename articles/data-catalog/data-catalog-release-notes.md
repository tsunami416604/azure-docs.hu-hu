---
title: Az Azure Data Catalog kibocsátási megjegyzései
description: Az Azure Data Catalog kibocsátási megjegyzéseit.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 60c5b7b55e417a5703010ea34cf75dcb20146c37
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531679"
---
# <a name="azure-data-catalog-release-notes"></a>Az Azure Data Catalog kibocsátási megjegyzései
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Az Azure Data Catalog kibocsátási megjegyzései a 2015. November 20.
### <a name="opening-data-sources-in-power-bi-desktop"></a>A Power BI Desktopban való megnyitásakor adatforrások
A "Megnyitás a Power BI Desktop" lehetőséget a **Azure Data Catalog** portál, felhasználók jelentkezhet a Power BI Desktop alkalmazásban két problémák egyike:

* "Nem sikerült, nyissa meg a dokumentum" címmel egy párbeszédpanel jelenik meg
* Megnyílik a Power BI Desktop alkalmazás, de a fájl úgy tűnik, hogy üres

Az egyes esetekben a probléma megoldható letöltésével és telepítésével a Power BI Desktop legújabb verzióját [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Az Azure Data Catalog kibocsátási megjegyzései a 13, 2015 November
### <a name="registering-and-connecting-to-teradata"></a>Regisztrálás és csatlakozás a Teradata
Amikor csatlakozik a Teradata adatforrásokat felhasználók telepíteni kell a megfelelő Teradata ODBC-illesztőt, amelyek megfelelnek a használt szoftver bitszámértékének (32 bites vagy 64 bites).

ADC kiadás dátumot, a legutóbbi [windows (verzió 15.10) készült Teradata ODBC-illesztő](https://downloads.teradata.com/download/connectivity/odbc-driver/windows) kompatibilis az Office 2013, de nem az Office 2016-tal.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Az Azure Data Catalog kibocsátási megjegyzései a 13, 2015 július
### <a name="registering-and-connecting-to-oracle-database"></a>Regisztráció és Oracle-adatbázishoz való kapcsolódáskor
Amikor felhasználók telepíteni kell a megfelelő Oracle-illesztőprogramokat, amelyek megfelelnek a használt szoftver bitszámértékének (32 bites vagy 64 bites) Oracle-adatbázis adatforrások csatlakozik.

* Ha 32 bites Windows rendszert futtató számítógépeken Oracle-adatforrások regisztrálása, a 32 bites Oracle illesztőprogramok fogja használni
* Ha 64 bites Windows rendszert futtató számítógépeken Oracle-adatforrások regisztrálása, a 64 bites Oracle illesztőprogramok fogja használni
* Forrásokhoz való kapcsolódáskor Oracle adatok az Excel használatával a Microsoft Office 32 bites verzióját futtató számítógépen, beleértve a Windows 64 bites, a 32 bites Oracle illesztőprogramok fogja használni
* Oracle-adatforrások a Microsoft Office 64 bites verzióját futtató számítógépen az Excel használatával való kapcsolódáskor a 64 bites Oracle illesztőprogramok fogja használni

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Regisztrálás és csatlakozás a SQL Server Reporting Services
Az SQL Server Reporting Services (SSRS) adatforrások támogatása jelenleg korlátozott, csak a natív módú kiszolgálók. A SharePoint-módú SSRS támogatása a rendszer felveszi egy későbbi kiadástól kezdve.

### <a name="opening-data-assets-in-excel"></a>Adategységek megnyitása az Excelben
Adategységek a Microsoft Excel megnyitásakor a **Azure Data Catalog** portál, előfordulhat, hogy megkérdezi a felhasználókat az egy **Microsoft Excel biztonsági figyelmeztetés** párbeszédpanel bezárásához. Ez a standard szintű, az elvárt viselkedést, és a felhasználók is kiválaszthatják **engedélyezése** folytatásához.

További információkért lásd: [engedélyezése vagy letiltása az információk a hivatkozásokról és a gyanús webhelyekről származó fájlokat a biztonsági riasztások](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy- és a házirend konfigurációját és az adatforrás regisztrációja
Felhasználók előfordulhatnak olyan helyzet, amelyben tudjon jelentkezni az Azure Data Catalog portál, de amikor azok megkísérlik az jelentkezzen be az adatforrás-regisztráló eszköz tapasztalnak, egy hibaüzenet, amely megakadályozza, hogy a bejelentkezés.

A probléma megoldásához a két lehetséges oka is van:

**1. ok: Az Active Directory összevonási szolgáltatások konfigurálása** az adatforrás-regisztráló eszköz használja az űrlapos hitelesítés Active Directorybeli felhasználói bejelentkezések ellenőrzése. A sikeres bejelentkezéshez az űrlapos hitelesítés engedélyezni kell a globális hitelesítési házirend egy Active Directory-rendszergazda.

Bizonyos esetekben a hiba oka lehet csak akkor, amikor a felhasználó a vállalati hálózaton van, vagy csak akkor, amikor a felhasználó a vállalati hálózaton kívülről érkező csatlakozik. A globális hitelesítési házirend lehetővé teszi, hogy a hitelesítési módszerek külön-külön engedélyezni kell az intranetes és extranetes kapcsolatokhoz. Bejelentkezési hiba akkor fordulhat elő, ha a hálózat, amelyről csatlakozik, a felhasználó nincs engedélyezve az űrlapos hitelesítés.

További információkért lásd: [hitelesítési házirendek konfigurálása](https://technet.microsoft.com/library/dn486781.aspx).

**2. ok: Hálózati proxy konfigurációs** a vállalati hálózat proxykiszolgálót használ, ha a regisztrációs eszköz nem lehet csatlakozni az Azure Active Directoryhoz a proxyn keresztül. Felhasználók is biztosítja, hogy a regisztrációs eszköz az eszköz konfigurációs fájl szerkesztésével ebben a szakaszban a fájl hozzáadása:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Keresse meg a RegistrationTool.exe.config fájlt, indítsa el a regisztrációs eszköz, és nyissa meg a Windows Feladatkezelő segédprogramot. Feladatkezelő Részletek lapján kattintson a jobb gombbal a RegistrationTool.exe, és válassza ki a fájlt tartalmazó mappa megnyitása a helyi menüből.
