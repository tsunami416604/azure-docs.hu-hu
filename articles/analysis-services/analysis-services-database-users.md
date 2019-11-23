---
title: Adatbázis-szerepkörök és-felhasználók kezelése a Azure Analysis Servicesban | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az adatbázis-szerepköröket és a felhasználókat egy Azure-beli Analysis Services-kiszolgálón.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9d1f3387fcea732e002689a4cdeaaf1d50d8a56f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147009"
---
# <a name="manage-database-roles-and-users"></a>Adatbázis-szerepkörök és-felhasználók kezelése

A modell adatbázis szintjén minden felhasználónak egy szerepkörhöz kell tartoznia. A szerepkörök meghatározott engedélyekkel rendelkező felhasználókat határoznak meg a modell adatbázisához. A szerepkörhöz hozzáadott összes felhasználónak vagy biztonsági csoportnak rendelkeznie kell egy, a-kiszolgálóval megegyező előfizetésben lévő fiókkal egy Azure AD-bérlőben. 

A szerepkörök definiálása eltérő lehet a használt eszköztől függően, de a hatás ugyanaz.

A szerepkör engedélyei a következők:
*  **Rendszergazda** – a felhasználók teljes körű engedélyekkel rendelkeznek az adatbázishoz. A rendszergazdai engedélyekkel rendelkező adatbázis-szerepkörök eltérnek a kiszolgálói rendszergazdáktól.
*  **Folyamat** – a felhasználók csatlakozhatnak az adatbázishoz, és elvégezhetik a folyamatokat, és elemezhetik a modell adatbázis-adataikat.
*  **Olvasási** – a felhasználók egy ügyfélalkalmazás segítségével csatlakozhatnak a modell adatbázis-adatbázisaihoz, és elemezhetik azokat.

Táblázatos modell projekt létrehozásakor szerepköröket hozhat létre, és felhasználókat vagy csoportokat adhat hozzá ezekhez a szerepkörökhöz a Visual Studióban Analysis Services projektek használatával. A kiszolgálókon való üzembe helyezéskor SQL Server Management Studio (SSMS), [Analysis Services PowerShell-parancsmagok](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)vagy [táblázatos modell parancsnyelv](https://docs.microsoft.com/bi-reference/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL) használatával adhat hozzá vagy távolíthat el szerepköröket és felhasználói tagokat.

A **biztonsági csoportoknak** e- [mail-kompatibilisnek](https://docs.microsoft.com/exchange/recipients-in-exchange-online/manage-mail-enabled-security-groups) kell lenniük a `MailEnabled` tulajdonsággal, amely `True`re van beállítva. Ha e-mail-cím alapján ad meg egy csoportot, használja a `obj:groupid@tenantid`.


## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Szerepkörök és felhasználók hozzáadása vagy kezelése a Visual Studióban  
  
1.  A **táblázatos modell Explorerben**kattintson a jobb gombbal a **szerepkörök**elemre.  
  
2.  A **Szerepkörkezelőben** kattintson az **Új** lehetőségre.  
  
3.  Adja meg a szerepkör nevét.  
  
     Alapértelmezés szerint a rendszer az alapértelmezett szerepkör nevét fokozatosan számozza az egyes új szerepkörökhöz. Javasoljuk, hogy adjon meg egy nevet, amely egyértelműen azonosítja a tag típusát, például: pénzügyi vezetők vagy emberi erőforrások szakemberei.  
  
4.  Válasszon egyet a következő engedélyek közül:  
  
    |Engedély|Leírás|  
    |----------------|-----------------|  
    |**Nincsenek**|A tagok nem módosíthatják a modell sémáját, és nem tudnak adatlekérdezést végrehajtani.|  
    |**Olvasás**|A tagok adatlekérdezéseket végezhetnek (a sorok szűrőinek alapján), de nem módosíthatják a modell sémáját.|  
    |**Olvasás és feldolgozás**|A tagok adatlekérdezéseket végezhetnek (a sor szintű szűrők alapján), és futtathatják a folyamatokat, és az összes műveletet feldolgozzák, de nem módosíthatják a modell sémáját.|  
    |**Folyamat**|A tagok az összes művelet feldolgozását és feldolgozását is futtathatják. A modell sémája nem módosítható, és nem lehet adatlekérdezést végrehajtani.|  
    |**Rendszergazda**|A tagok módosíthatják a modell sémáját, és lekérhetik az összes adatlekérdezést.|   
  
5.  Ha a létrehozandó szerepkör olvasási vagy olvasási és feldolgozási engedéllyel rendelkezik, akkor a DAX-képletek használatával hozzáadhat sorokhoz tartozó szűrőket. Kattintson a **sorok szűrők** fülre, majd jelöljön ki egy táblát, majd kattintson a **DAX-szűrő** mezőre, majd írjon be egy DAX-képletet.
  
6.  Kattintson a **tagok** > **külső hozzáadása**elemre.  
  
8.  A **külső tag hozzáadása**területen adja meg a bérlői Azure ad-beli felhasználókat vagy csoportokat e-mail-cím szerint. Miután rákattintott az OK gombra, és lezárta a szerepkör-kezelőt, a szerepkörök és szerepkörök tagjai megjelennek a táblázatos modell Explorerben. 
 
     ![Szerepkörök és felhasználók a táblázatos modell Explorerben](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Telepítse a Azure Analysis Services-kiszolgálóra.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Szerepkörök és felhasználók hozzáadása vagy kezelése a SSMS-ben

Szerepkörök és felhasználók egy központilag telepített modell-adatbázishoz való hozzáadásához kiszolgálói rendszergazdaként vagy már egy rendszergazdai engedélyekkel rendelkező adatbázis-szerepkörben kell csatlakoznia a kiszolgálóhoz.

1. Az Object Exporer kattintson a jobb gombbal a **szerepkörök** > **Új szerepkör**elemre.

2. A **szerepkör létrehozása**területen adja meg a szerepkör nevét és leírását.

3. Válasszon ki egy engedélyt.

   |Engedély|Leírás|  
   |----------------|-----------------|  
   |**Teljes hozzáférés (rendszergazda)**|A tagok módosíthatják a modell sémáját, feldolgozhatják és lekérhetik az összes adatlekérdezést.| 
   |**Adatbázis feldolgozása**|A tagok az összes művelet feldolgozását és feldolgozását is futtathatják. A modell sémája nem módosítható, és nem lehet adatlekérdezést végrehajtani.|  
   |**Olvasás**|A tagok adatlekérdezéseket végezhetnek (a sorok szűrőinek alapján), de nem módosíthatják a modell sémáját.|  
  
4. Kattintson a **tagság**elemre, majd adjon meg egy felhasználót vagy csoportot a bérlő Azure ad-ban e-mail-cím szerint.

     ![Felhasználó hozzáadása](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Ha a létrehozandó szerepkör olvasási engedéllyel rendelkezik, akkor egy DAX-képlet használatával adhat hozzá sor szűrőket. Kattintson a **sorok szűrők**elemre, jelöljön ki egy táblát, majd írjon be egy DAX-képletet a **DAX-szűrő** mezőbe. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Szerepkörök és felhasználók hozzáadása TMSL-parancsfájl használatával

A TMSL parancsfájlt a SSMS XMLA ablakában vagy a PowerShell használatával futtathatja. Használja a [CreateOrReplace](https://docs.microsoft.com/bi-reference/tmsl/createorreplace-command-tmsl) parancsot és a [roles](https://docs.microsoft.com/bi-reference/tmsl/roles-object-tmsl) objektumot.

**Példa TMSL-parancsfájlra**

Ebben a példában egy B2B külső felhasználót és egy csoportot adnak hozzá az elemző szerepkörhöz a SalesBI-adatbázis olvasási engedélyeivel. A külső felhasználónak és a csoportnak ugyanabban a bérlői Azure AD-ben kell lennie.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Szerepkörök és felhasználók hozzáadása a PowerShell használatával

A [SQLServer](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) modul a feladat-specifikus adatbázis-kezelési parancsmagokat és az általános célú meghívó-ASCmd parancsmagot biztosít, amely egy táblázatos modell parancsnyelv-(TMSL-) lekérdezését vagy parancsfájlját fogadja el. Az adatbázis-szerepkörök és a felhasználók kezeléséhez a következő parancsmagok használhatók.
  
|Parancsmag|Leírás|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Vegyen fel egy tagot egy adatbázis-szerepkörbe.| 
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Egy tag eltávolítása egy adatbázis-szerepkörből.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|TMSL-szkript végrehajtása.|

## <a name="row-filters"></a>Sorok szűrői  

A sorok szűrői határozzák meg, hogy egy adott szerepkör tagjai hogyan tudják lekérdezni egy tábla sorait. A sorokhoz tartozó szűrők a modell minden egyes táblájához a DAX-képletek használatával definiálhatók.  
  
A sorok szűrői csak olvasási és olvasási és feldolgozási engedélyekkel rendelkező szerepkörökhöz definiálhatók. Alapértelmezés szerint, ha egy adott táblához nincs megadva egy sor szűrő, a tagok a tábla összes sorát lekérhetik, kivéve, ha a szűrés egy másik táblából történik.
  
 A sorok szűrői egy DAX-képletet igényelnek, amelyet igaz/hamis értékre kell kiértékelni az adott szerepkör tagjai által lekérdezhető sorok definiálásához. Nem lehet lekérdezni a DAX-képletben nem szereplő sorokat. Például az ügyfelek tábla a következő sorcsoport-kifejezéssel, *= ügyfelek [ország] = "USA"* , az értékesítési szerepkör tagjai csak az USA-beli ügyfeleket láthatják.  
  
A sorok szűrői a megadott sorokra és a kapcsolódó sorokra vonatkoznak. Ha egy táblának több kapcsolata van, a szűrők az aktív kapcsolat biztonságát alkalmazzák. A sorok szűrői a kapcsolódó táblákhoz definiált más sorokkal vannak összemetszve, például:  
  
|Tábla|DAX-kifejezés|  
|-----------|--------------------|  
|Régió|=Region[Country]="USA"|  
|ProductCategory|= ProductCategory [név] = "kerékpárok"|  
|Tranzakciók|=Transactions[Year]=2016|  
  
 A háló hatására a tagok lekérhetik az adatsorokat, ahol az ügyfél az USA-ban található, a termék kategóriája kerékpárok, az év pedig 2016. A felhasználók nem tudják lekérdezni az Egyesült Államokon kívüli tranzakciókat, a nem kerékpárokat vagy a 2016-es tranzakciókat, kivéve, ha azok egy másik szerepkör tagja, amely ezeket az engedélyeket megadja.
  
 A szűrő, *= false ()* használatával megtagadhatja a hozzáférést egy teljes tábla összes sorához.

## <a name="next-steps"></a>Következő lépések

  [Kiszolgáló-rendszergazdák kezelése](analysis-services-server-admins.md)   
  [Azure Analysis Services kezelése a PowerShell-lel](analysis-services-powershell.md)  
  [Táblázatos modell programozási nyelv (TMSL) – dokumentáció](https://docs.microsoft.com/bi-reference/tmsl/tabular-model-scripting-language-tmsl-reference)

