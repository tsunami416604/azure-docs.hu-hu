---
title: "Az Azure Data Catalog kibocsátási megjegyzései |} Microsoft Docs"
description: "Kibocsátási megjegyzések az Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 249c3a690638bb91d060443d591d8e52791f50ae
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="azure-data-catalog-release-notes"></a>Az Azure Data Catalog kibocsátási megjegyzései
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Az Azure Data Catalog kibocsátási megjegyzések a 2015. November 20, a
### <a name="opening-data-sources-in-power-bi-desktop"></a>A Power BI Desktop nyitó adatforrások
Ha az "Nyissa meg a Power BI Desktop" elemet a **Azure Data Catalog** portálon felhasználók szembesülhetnek a Power BI Desktop-kérelemben két problémák egyikét:

* A "Nem nyissa meg a dokumentumhoz" címmel párbeszédpanel
* A Power BI Desktop alkalmazás nyílik meg, de a fájl úgy tűnik, hogy üres

Az egyes esetekben a probléma megoldásához érdemes letölti és telepíti a legújabb verzióját a Power BI Desktop [powerbi.com webhelyen](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Az Azure Data Catalog kibocsátási megjegyzések a 2015. November 13, a
### <a name="registering-and-connecting-to-teradata"></a>Regisztrálja, és csatlakozik a teradata rendszerhez
Amikor Teradata adatforrások felhasználók a megfelelő Teradata ODBC-illesztőprogram van telepítve, amely egyezik a (32 bites vagy 64 bites) a használt szoftver csatlakozik.

LÉPETT kiadási dátumot, a legutóbbi [Teradata ODBC-illesztőprogram (verzió: 15.10) Windows](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) kompatibilis Office 2013, de nem Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Az Azure Data Catalog kibocsátási megjegyzések a 2015. július 13 a
### <a name="registering-and-connecting-to-oracle-database"></a>Regisztráció és Oracle-adatbázishoz szeretne csatlakozni
Amikor a felhasználók a megfelelő Oracle-illesztőprogramokat, amely egyezik a (32 bites vagy 64 bites) a használt a szoftverfrissítések által telepített adatforrások Oracle-adatbázishoz való kapcsolódás.

* 32 bites Windows rendszert futtató kiszolgálón tárolt olyan adatforrások Oracle regisztrálásakor a 32 bites Oracle illesztőprogramok fogja használni
* 64 bites Windows rendszert futtató kiszolgálón tárolt olyan adatforrások Oracle regisztrálásakor a 64 bites Oracle illesztőprogramok fogja használni
* Kapcsolódás Oracle-adatforrások Excel használatával a Microsoft Office 32 bites verzióját futtató számítógépen, ha a 64 bites Windows esetén, beleértve a 32 bites Oracle illesztőprogramok fogja használni
* Oracle-adatforrások Excel használatával a Microsoft Office 64 bites verzióját futtató számítógépen való kapcsolódáskor használandó a 64 bites Oracle illesztőprogramok

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Regisztráció és SQL Server Reporting Services csatlakozik
Az SQL Server Reporting Services (SSRS) adatforrásokat korlátozódik jelenleg csak a natív üzemmódra kiszolgálók. A SharePoint módban SSRS támogatása egy későbbi kiadásban lesz hozzáadva.

### <a name="opening-data-assets-in-excel"></a>Az Excel megnyitásakor adategységeket
A Microsoft Excel az adategységek megnyitásakor a **Azure Data Catalog** portálon kéri a egy **Microsoft Excel biztonsági figyelmeztetés** párbeszédpanel. Standard szintű, ami normális működés, és a felhasználók is kiválaszthat **engedélyezése** folytatja.

További információkért lásd: [engedélyezheti vagy tilthatja le a biztonsági riasztások hivatkozások és a gyanús webhelyekről származó fájlokat](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy- és házirend-konfiguráció és adatok adatforrás regisztrálása
Felhasználók is talál, ahol bejelentkezés az Azure Data Catalog-portált, de amikor megpróbálják jelentkezzen be az adatforrás-regisztráló eszköz olyan hibaüzenetet, amely megakadályozza, hogy bejelentkezzen szembesülnek.

A probléma megoldásához két lehetséges oka is van:

**1. ok: Az Active Directory összevonási szolgáltatások konfigurációs** az adatforrás-regisztráló eszköz űrlapok-hitelesítést használ a felhasználói bejelentkezés Active Directory általi érvényesítésére. Sikeres bejelentkezés az űrlapos hitelesítés engedélyezni kell a globális hitelesítési házirend Active Directory-rendszergazda.

Bizonyos esetekben a hiba oka lehet csak akkor, ha a felhasználó a vállalati hálózaton van, vagy csak akkor, ha a felhasználó a kapcsolódik a vállalati hálózaton kívülről. A globális hitelesítési házirend lehetővé teszi, hogy a hitelesítési módszerek külön-külön engedélyezni kell az intranetes és extranetes kapcsolatok. Bejelentkezési hiba akkor fordulhat elő, ha a hálózathoz, amelyről a felhasználó kapcsolódik nincs engedélyezve az űrlapos hitelesítés.

További információkért lásd: [hitelesítési házirendek konfigurálása](https://technet.microsoft.com/library/dn486781.aspx).

**2. ok: Hálózati proxykonfigurációt** a vállalati hálózat proxykiszolgálót használ, ha a frissítésregisztráló eszköz nem lehet csatlakozni az Azure Active Directoryba a proxyn keresztül. Felhasználók is biztosítja, hogy a frissítésregisztráló eszköz az eszköz konfigurációs fájlt, ez a szakasz a fájl hozzáadása:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Keresse meg a RegistrationTool.exe.config fájlt, indítsa el a regisztrációs eszköz, és nyissa meg a Windows Feladatkezelő segédprogramot. A Feladatkezelő Részletek lapján kattintson a jobb gombbal a RegistrationTool.exe, és válassza ki a fájl helyének megnyitása a legördülő menüből.
