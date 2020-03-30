---
title: Több-bérlős webalkalmazás minta | Microsoft dokumentumok
description: Építészeti áttekintéseket és tervezési mintákat találhat, amelyek leírják, hogyan valósíthat meg egy több-bérlős webalkalmazást az Azure-ban.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: d3e267eab056589ed38c436620dd0db185291da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425901"
---
# <a name="multitenant-applications-in-azure"></a>Több-bérlős alkalmazások az Azure-ban
A több-bérlős alkalmazás egy megosztott erőforrás, amely lehetővé teszi a "felhasználók külön bérlők" az alkalmazás megtekintéséhez, mintha a saját. Egy tipikus forgatókönyv, amely alkalmas arra, hogy egy több-bérlős alkalmazás az egyik, amelyben az alkalmazás összes felhasználója a különböző bérlők érdemes testre szabni a felhasználói élményt, de egyébként ugyanazokkal az alapvető üzleti követelményekkel rendelkezik. Nagy méretű több-bérlős alkalmazások például az Office 365, a Outlook.com és a visualstudio.com.

Az alkalmazásszolgáltató szempontjából a több-bérlős adatok előnyei főként a működési és költséghatékonyságra vonatkoznak. Az alkalmazás egyik verziója számos bérlő/ügyfél igényeit elégítheti ki, lehetővé téve a rendszerfelügyeleti feladatok, például a figyelés, a teljesítményhangolás, a szoftverkarbantartás és az adatbiztonsági mentések összevonását.

Az alábbi lista a szolgáltató szempontjából a legjelentősebb célokat és követelményeket tartalmazza.

* **Kiépítése**: Képesnek kell lennie arra, hogy az alkalmazás új bérlők kiépítése.  A nagy számú bérlővel rendelkező több-bérlős alkalmazások esetében általában szükség van a folyamat automatizálására az önkiszolgáló kiépítés engedélyezésével.
* **Karbantarthatóság:** Képesnek kell lennie az alkalmazás frissítésére és egyéb karbantartási feladatok végrehajtására, miközben több bérlő használja.
* **Figyelés**: Mindig figyelnie kell az alkalmazást, hogy azonosíthassa a problémákat, és elháríthassa azokat. Ez magában foglalja az egyes bérlők által az alkalmazás használatát figyelése.

Egy megfelelően megvalósított több-bérlős alkalmazás a következő előnyöket biztosítja a felhasználók számára.

* **Elkülönítés**: Az egyes bérlők tevékenységei nem befolyásolják az alkalmazás más bérlők általi használatát. A bérlők nem férhetnek hozzá egymás adataihoz. Úgy tűnik, hogy a bérlő, mintha kizárólagos használata az alkalmazás.
* **Elérhetőség:** Az egyes bérlők azt szeretnék, hogy az alkalmazás folyamatosan elérhető legyen, esetleg egy SLA-ban meghatározott garanciákkal. Ismét a tevékenységek más bérlők nem befolyásolja az alkalmazás rendelkezésre állását.
* **Méretezhetőség**: Az alkalmazás az egyes bérlők igényeinek megfelelően méretezhető. A jelenléte és a műveletek más bérlők nem befolyásolja az alkalmazás teljesítményét.
* **Költségek**: A költségek alacsonyabbak, mint egy dedikált, egy-bérlős alkalmazás futtatása, mivel a több-bérlős lehetővé teszi az erőforrások megosztását.
* **Testreszabhatóság**. Az a képesség, hogy testre az alkalmazás egy adott bérlő különböző módokon, mint például a szolgáltatások hozzáadása vagy eltávolítása, a színek és logók módosítása, vagy akár hozzá a saját kódot vagy parancsfájlt.

Röviden, bár számos szempontot figyelembe kell venni, hogy egy jól skálázható szolgáltatás, vannak is számos célok és követelmények, amelyek közösek a több-bérlős alkalmazások. Előfordulhat, hogy egyes forgatókönyvek nem relevánsak, és az egyes célok és követelmények fontossága minden esetben eltérő lesz. A több-bérlős alkalmazás szolgáltatójaként olyan célokat és követelményeket is megfog adni, mint például a bérlő céljainak és követelményeinek teljesítése, a nyereségesség, a számlázás, a több szolgáltatási szint, a kiépítés, a karbantarthatóság figyelése és az automatizálás.

A több-bérlős alkalmazások további tervezési szempontjairól a [Több-bérlős alkalmazás üzemeltetése az Azure-ban][Hosting a Multi-Tenant Application on Azure]című témakörben talál további információt. A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Az Azure számos olyan szolgáltatást kínál, amelyek lehetővé teszik a több-bérlős rendszer tervezésekor felmerülő fő problémák kezelését.

**Elkülönítés**

* Webhely-bérlők szegmense SSL-kommunikációval vagy anélkül rendelkező gazdagépfejlécek szerint
* Webhely-bérlők szegmense lekérdezési paraméterek szerint
* Webszolgáltatások a feldolgozói szerepkörökben
  * Feldolgozói szerepkörök, amelyek általában az alkalmazás háttérrendszerének adatait dolgozzák fel.
  * Webes szerepkörök, amelyek általában az alkalmazások előtérként működnek.

**Tárterület**

Az adatkezelés, például az Azure SQL Database vagy az Azure Storage-szolgáltatások, például a Table szolgáltatás, amely nagy mennyiségű strukturálatlan adat tárolásához nyújt szolgáltatásokat, valamint a Blob szolgáltatás, amely nagy mennyiségű strukturálatlan szöveg vagy bináris adatok, például videó, hang és képek.

* Több-bérlős adatok védelme az SQL Database bérlőnkénti SQL Server bejelentkezések.
* Az Azure Tables for Application Resources használatával egy tárolószintű hozzáférési szabályzat megadásával módosíthatja az engedélyeket anélkül, hogy új URL-címeket kellene kiadnia a megosztott hozzáférésű aláírásokkal védett erőforrásokhoz.
* Az Azure-várólisták az alkalmazás-erőforrások hoz az Azure-várólisták gyakran használják a bérlők nevében történő feldolgozás, de a kiépítéshez vagy felügyelethez szükséges munka elosztására is használható.
* Az alkalmazás-erőforrások szolgáltatásbusz-várólistái, amelyek leküldéses munkát egy megosztott szolgáltatás, használhatja egyetlen várólistában, ahol minden bérlő feladó csak rendelkezik engedélyekkel (származik jogcímek által kiadott ACS), hogy leküldéses, hogy a várólistába, míg csak a fogadók a szolgáltatás engedélyt a több bérlőtől érkező adatok várólistából való lekérése.

**Csatlakozási és biztonsági szolgáltatások**

* Az Azure Service Bus, egy üzenetkezelési infrastruktúra, amely az alkalmazások között helyezkedik el, lehetővé téve számukra, hogy az üzenetek cseréje lazán összekapcsolt módon a jobb méretezés és rugalmasság érdekében.

**Hálózati szolgáltatások**

Az Azure számos olyan hálózati szolgáltatást kínál, amelyek támogatják a hitelesítést, és javítják a hosztolt alkalmazások kezelhetőségét. Ezek a szolgáltatások a következők:

* Az Azure Virtual Network lehetővé teszi a virtuális magánhálózatok (VPN-ek) kiépítését és kezelését az Azure-ban, valamint biztonságos annektálást biztosít a helyszíni informatikai infrastruktúrával.
* A Virtual Network Traffic Manager lehetővé teszi a bejövő forgalom több üzemeltetett Azure-szolgáltatás közötti terhelését, függetlenül attól, hogy ugyanabban az adatközpontban vagy a világ különböző adatközpontjaiban futnak.
* Az Azure Active Directory (Azure AD) egy modern, REST-alapú szolgáltatás, amely identitáskezelési és hozzáférés-vezérlési lehetőségeket biztosít a felhőalapú alkalmazások számára. Az Azure AD alkalmazás-erőforrások használatával egyszerűen hitelesítheti és engedélyezheti a felhasználók számára, hogy hozzáférjenek a webes alkalmazásokhoz és szolgáltatásokhoz, miközben lehetővé teszi a hitelesítés és az engedélyezés funkcióinak a kódból való kivonását.
* Az Azure Service Bus biztonságos üzenetküldési és adatfolyam-szolgáltatást biztosít az elosztott és hibrid alkalmazásokhoz, például az Azure által üzemeltetett alkalmazások és helyszíni alkalmazások és szolgáltatások közötti kommunikációhoz, összetett tűzfal és biztonság nélkül Infrastruktúrák. A Service Bus Relay az alkalmazás-erőforrások a végpontokként elérhető szolgáltatások eléréséhez a bérlőhöz tartozhat (például a rendszeren kívül üzemeltetett, például a helyszínen), vagy lehet, hogy kifejezetten a bérlő számára kiosztott szolgáltatások (mert érzékeny, bérlő-specifikus adatok utaznak át rajtuk).

**Erőforrások kiépítése**

Az Azure számos lehetőséget kínál az alkalmazás új bérlők kiépítésére. A nagy számú bérlővel rendelkező több-bérlős alkalmazások esetében általában szükség van a folyamat automatizálására az önkiszolgáló kiépítés engedélyezésével.

* A feldolgozói szerepkörök lehetővé teszik a bérlői erőforrások (például amikor egy új bérlő regisztrál vagy megszakítja), mérőszámok at gyűjt a mérési használatra, és skálázáskezelése egy bizonyos ütemezés szerint vagy a kulcsteljesítmény küszöbértékeinek átlépése esetén. Mutatók. Ugyanez a szerepkör is használható a frissítések és frissítések kiküldése a megoldáshoz.
* Az Azure Blobok számítási vagy előzetesen inicializált tárolási erőforrások kiépítésére használhatók az új bérlők számára, miközben tárolószintű hozzáférési szabályzatokat biztosítanak a számítási szolgáltatáscsomagok, a vHD-rendszerképek és egyéb erőforrások védelmére.
* Az SQL Database-erőforrások bérlőhöz való kiépítésének lehetőségei a következők:
  
  * DDL parancsfájlokban vagy beágyazott erőforrásokként a szerelvényeken belül.
  * Az SQL Server 2008 R2 DAC csomagok programozott módon vannak telepítve.
  * Másolás fő referenciaadatbázisból.
  * Az adatbázis importálása és exportálása parancsáb fájlból történő kiépítése.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716
