---
title: Adatbázis-szerepkörök és felhasználók kezelése az Azure Analysis Services ben | Microsoft dokumentumok
description: Ismerje meg, hogyan kezelheti az adatbázis-szerepköröket és a felhasználókat az Azure-beli Analysis Services-kiszolgálón.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b7e3cc2b9d35eafcb875efa167821a8e9ad80146
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454203"
---
# <a name="manage-database-roles-and-users"></a>Adatbázis-szerepkörök és felhasználók kezelése

A modelladatbázis szintjén minden felhasználónak egy szerepkörhöz kell tartoznia. A szerepkörök a modelladatbázishoz adott engedélyekkel rendelkező felhasználókat határoznak meg. A szerepkörhöz hozzáadott bármely felhasználónak vagy biztonsági csoportnak rendelkeznie kell egy azure-beli AD-bérlőben lévő fiókkal a kiszolgálóval azonos előfizetésben. 

A szerepkörök definiálásának lépése a használt eszköztől függően eltérő, de a hatás ugyanaz.

A szerepkörengedélyek a következők:
*  **Rendszergazda** – A felhasználók teljes körű engedélyekkel rendelkeznek az adatbázishoz. A rendszergazdai engedélyekkel rendelkező adatbázis-szerepkörök eltérnek a kiszolgáló rendszergazdáitól.
*  **Folyamat** – A felhasználók csatlakozhatnak az adatbázishoz, és folyamatműveleteket hajthatnak végre, és elemezhetik a modelladatbázis-adatokat.
*  **Olvasás** – A felhasználók egy ügyfélalkalmazás segítségével csatlakozhatnak a modelladatbázis-adatokhoz, és elemezhetik azokat.

Táblázatos modellprojekt létrehozásakor szerepköröket hozhat létre, és felhasználókat vagy csoportokat adhat hozzá ezekhez a szerepkörökhöz a Visual Studio szerepkör-kezelőjével az Analysis Services-projektekkel. Kiszolgálóra telepítve az SQL Server Management Studio (SSMS), [az Analysis Services PowerShell-parancsmagjai](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)vagy [a TMSL (Táblázatos modellparancsfájl-nyelv)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) használatával adja hozzá vagy távolítsa el a szerepköröket és a felhasználói tagokat.

**Biztonsági csoport**hozzáadásakor `obj:groupid@tenantid`használja a használatát.

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Szerepkörök és felhasználók hozzáadása és kezelése a Visual Studióban  
  
1.  A **Táblázatos modellkezelőben**kattintson a jobb gombbal **a Szerepkörök**elemre.  
  
2.  A **Szerepkörfőben**kattintson az **Új**gombra.  
  
3.  Írja be a szerepkör nevét.  
  
     Alapértelmezés szerint az alapértelmezett szerepkör neve minden új szerepkörhöz növekményesen van számozva. Javasoljuk, hogy írjon be egy nevet, amely egyértelműen azonosítja a tag típusát, például pénzügyi menedzsereket vagy emberi erőforrás-szakértőket.  
  
4.  Válasszon az alábbi engedélyek közül:  
  
    |Engedély|Leírás|  
    |----------------|-----------------|  
    |**Nincs**|A tagok nem olvashatják és nem módosíthatják a modellsémát, és nem tudnak adatokat lekérdezni.|  
    |**Olvasás**|A tagok lekérdezhetik az adatokat (sorszűrők alapján), de nem módosíthatják a modellsémát.|  
    |**Olvasás és feldolgozás**|A tagok lekérdezhetik az adatokat (sorszintű szűrők alapján), és futtathatják az Összes művelet feldolgozása és feldolgozása parancsot, de nem módosíthatják a modellsémát.|  
    |**Folyamat**|A tagok futtathatják a Folyamat és az Összes művelet feldolgozását. A modellséma nem olvasható vagy módosítható, és nem lehet adatokat lekérdezni.|  
    |**Rendszergazda**|A tagok módosíthatják a modellsémát, és lekérdezhetik az összes adatot.|   
  
5.  Ha a létrehozás alatt létrekívánt szerepkör olvasási vagy olvasási és feldolgozási engedéllyel rendelkezik, a DAX-képlet segítségével hozzáadhat sorszűrőket. Kattintson a **Sorszűrők** fülre, jelöljön ki egy táblázatot, majd kattintson a **DAX-szűrő** mezőre, és írja be a DAX-képletet.
  
6.  Kattintson **a Tagok** > **hozzáadása külső gombra.**  
  
8.  A **Külső tag hozzáadása**mezőbe írja be a felhasználókat vagy csoportokat a bérlői Azure AD-be e-mail-cím szerint. Miután az OK gombra kattintott, és bezárta a Szerepkör-kezelőt, a szerepkörök és a szerepkörtagok megjelennek a táblázatos modellkezelőben. 
 
     ![Szerepkörök és felhasználók a táblázatos modellkezelőben](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Telepítse az Azure Analysis Services-kiszolgálóra.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Szerepkörök és felhasználók hozzáadása és kezelése az SSMS-ben

Ha szerepköröket és felhasználókat szeretne hozzáadni egy telepített modelladatbázishoz, kiszolgálórendszergazdaként vagy már rendszergazdai engedélyekkel rendelkező adatbázis-szerepkörben kell csatlakoznia a kiszolgálóhoz.

1. Az Objektumkivánsok csoportban kattintson a jobb gombbal **az Új szerepkör szerepkör** > **elemre.**

2. A **Szerepkör létrehozása mezőbe**írja be a szerepkör nevét és leírását.

3. Válasszon egy engedélyt.

   |Engedély|Leírás|  
   |----------------|-----------------|  
   |**Teljes hozzáférés (rendszergazda)**|A tagok módosíthatják a modellsémát, a folyamatot, és lekérdezhetik az összes adatot.| 
   |**Adatbázis feldolgozása**|A tagok futtathatják a Folyamat és az Összes művelet feldolgozását. A modellséma nem módosítható, és nem lehet adatokat lekérdezni.|  
   |**Olvasás**|A tagok lekérdezhetik az adatokat (sorszűrők alapján), de nem módosíthatják a modellsémát.|  
  
4. Kattintson **a Tagság gombra,** majd adjon meg egy felhasználót vagy csoportot a bérlői Azure AD-ben e-mail-cím szerint.

     ![Felhasználó hozzáadása](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Ha a létrehozási szerepkör olvasási engedéllyel rendelkezik, a DAX-képlet segítségével hozzáadhat sorszűrőket. Kattintson **a Sorszűrők gombra,** jelöljön ki egy táblát, majd írjon be egy DAX-képletet a **DAX-szűrő** mezőbe. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Szerepkörök és felhasználók hozzáadása TMSL-parancsfájl használatával

TMSL-parancsfájlt futtathat az XMLA-ablakban az SSMS-ben vagy a PowerShell használatával. Használja a [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) parancsot és a [Roles](https://docs.microsoft.com/analysis-services/tmsl/roles-object-tmsl) objektumot.

**Példa TMSL-parancsfájlra**

Ebben a példában egy B2B külső felhasználó és egy csoport kerül az Elemző szerepkörhöz olvasási engedélyekkel a SalesBI-adatbázishoz. A külső felhasználónak és a csoportnak is ugyanabban a bérlői Azure AD-ben kell lennie.

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

Az [SqlServer](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) modul feladatspecifikus adatbázis-kezelési parancsmagokat és általános célú Meghívás-ASCmd parancsmagát biztosít, amely elfogadja a táblázatos modellparancsfájl-írásnyelv (TMSL) lekérdezést vagy parancsfájlt. A következő parancsmagok az adatbázis-szerepkörök és a felhasználók kezelésére szolgálnak.
  
|Parancsmag|Leírás|
|------------|-----------------| 
|[Bővítmény-szerepkörtag](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Tag hozzáadása adatbázis-szerepkörhöz.| 
|[Eltávolítás-szerepkörtag](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Tag eltávolítása adatbázis-szerepkörből.|   
|[Meghívás-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|TMSL-parancsfájl végrehajtása.|

## <a name="row-filters"></a>Sorszűrők  

A sorszűrők határozzák meg, hogy egy tábla mely sorait kérdezhetik le egy adott szerepkör tagjai. A sorszűrők a modell minden táblájához DAX-képletek használatával vannak definiálva.  
  
A sorszűrők csak olvasási és olvasási és feldolgozási engedéllyel rendelkező szerepkörökhöz adhatók meg. Alapértelmezés szerint, ha egy adott táblához nincs megadva sorszűrő, a tagok lekérdezhetik a tábla összes sorát, kivéve, ha a keresztszűrés egy másik táblából történik.
  
 A sorszűrőkhez olyan DAX-képletszükséges, amelynek IGAZ/HAMIS értékre kell értékelnie az adott szerepkör tagjai által lekérdezhető sorokat. A DAX-képletben nem szereplő sorok nem kérdezhetők le. Például a Vevők tábla a következő sorszűrővel a következő : *=Vevők [Ország] = "USA"*, az Értékesítési szerepkör tagjai csak az USA-ban lévő vevőket láthatják.  
  
A sorszűrők a megadott sorokra és a kapcsolódó sorokra vonatkoznak. Ha egy táblának több kapcsolata van, a szűrők az aktív kapcsolat biztonságát alkalmazzák. A sorszűrőket a rendszer metszi a kapcsolódó táblákhoz definiált többi sorfájlkezelővel, például:  
  
|Tábla|DAX-kifejezés|  
|-----------|--------------------|  
|Régió|=Régió[Ország]="USA"|  
|ProductCategory|=Termékkategória[Név]="Kerékpárok"|  
|Tranzakciók|=Tranzakciók[Év]=2016|  
  
 A nettó hatás az, hogy a tagok lekérdezhetik az adatok sorait, ahol az ügyfél az USA-ban van, a termékkategória kerékpárok, és az év 2016. A felhasználók nem kérdezhetnek le tranzakciókat az USA-n kívül, nem kerékpárokat tartalmazó tranzakciókat, illetve nem 2016-ban lebonyolított tranzakciókat, kivéve, ha egy másik szerepkör tagja, amely megadja ezeket az engedélyeket.
  
 Az *=FALSE()* szűrővel megtagadhatja a hozzáférést a teljes tábla összes sorához.

## <a name="next-steps"></a>További lépések

  [Kiszolgálói rendszergazdák kezelése](analysis-services-server-admins.md)   
  [Az Azure Analysis Services kezelése a PowerShell-lel](analysis-services-powershell.md)  
  [Táblázatos modell parancsfájlnyelv (TMSL) – referencia](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)

