---
title: Adatbázis-szerepkörök és a felhasználók az Azure Analysis Servicesben kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az adatbázis-szerepkörök és a felhasználók az Analysis Services-kiszolgáló, az Azure-ban.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ab238e31ba022427dc55fec842f3e89e72fa530a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238879"
---
# <a name="manage-database-roles-and-users"></a>Adatbázis-szerepkörök és a felhasználók kezelése

A modell adatbázis szintjén minden felhasználó egy szerepkörhöz kell tartoznia. Szerepkörök definiálása a model adatbázishoz adott engedélyekkel rendelkező felhasználók. Minden olyan felhasználó vagy biztonsági csoportot hozzáadni egy szerepkörhöz az Azure AD-bérlő ugyanahhoz az előfizetéshez tartozik, mint a kiszolgáló-fiókkal kell rendelkeznie. 

Hogyan adhatja meg a szerepkörök attól függően változik, a használt eszköz, de a hatás megegyezik.

Szerepkör-engedélyek a következők:
*  **Rendszergazdai** -felhasználók az adatbázis teljes körű jogosultsággal rendelkezik. Rendszergazdai jogosultságokkal rendelkező adatbázis-szerepkörök nem azonosak a kiszolgálói rendszergazdák.
*  **Folyamat** -felhasználók csatlakozhat és folyamat műveleteket az adatbázison, és model adatbázis adatok elemzéséhez.
*  **Olvasási** -felhasználók egy ügyfélalkalmazás segítségével csatlakozhat, és a model adatbázis adatok elemzéséhez.

Táblázatosmodell-projekt létrehozásakor, hozhat létre szerepköröket, és a felhasználók vagy csoportok hozzáadása, ezeket a szerepköröket a szerepkörkezelővel az SSDT-ben. Telepítésekor egy kiszolgálóhoz az SSMS-használja [Analysis Services PowerShell-parancsmagok](https://msdn.microsoft.com/library/hh758425.aspx), vagy [táblázatos modell Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) hozzáadása vagy eltávolítása a szerepkörök és a felhasználói tagok (TMSL).

> [!NOTE]
> Biztonsági csoportok kell rendelkeznie a `MailEnabled` tulajdonság `True`.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Adja hozzá, vagy kezelheti a szerepköröket és a felhasználók az SSDT-ben  
  
1.  Az SSDT > **Táblázatosmodell-tallózóban**, kattintson a jobb gombbal **szerepkörök**.  
  
2.  A **Szerepkörkezelőben** kattintson az **Új** lehetőségre.  
  
3.  Írja be a szerepkör nevét.  
  
     Alapértelmezés szerint az alapértelmezett szerepkör neve növekményes számozott minden új szerepkörhöz. Javasoljuk, adjon meg egy nevet, amely egyértelműen azonosítja a tag típusa, például a pénzügyi igazgatók vagy az emberi erőforrások szakértőitől.  
  
4.  Válassza ki a következő engedélyekkel:  
  
    |Engedély|Leírás|  
    |----------------|-----------------|  
    |**Egyik sem**|A tagok nem módosítható, ha a, és adatokat nem lehet lekérdezni.|  
    |**Olvasás**|A tagok lekérdezheti az adatokat (Sorszűrők alapján), de nem módosíthatja a modellsémát.|  
    |**Olvasás és feldolgozás**|Tagok (alapján sorszintű szűrők) adatok és a futtatási folyamat és a folyamat minden műveletek lekérdezheti, de nem módosítható a modellsémát.|  
    |**Folyamat**|Tagok folyamat és a folyamat minden műveleteket is futtathat. Nem módosítható, ha a, és adatokat nem lehet lekérdezni.|  
    |**Rendszergazda**|A tagok modellsémát módosíthatja, és minden adat lekérdezése.|   
  
5.  Ha a szerepkör létrehozása rendelkezik-e olvasási vagy olvasás és feldolgozás engedélyt adhat hozzá Sorszűrők DAX-képlet használatával. Kattintson a **Sorszűrők** lapra, majd válasszon ki egy táblát, majd kattintson a **DAX-szűrő** mezőben, majd írja be egy DAX-képletet.
  
6.  Kattintson a **tagok** > **adja hozzá a külső**.  
  
8.  A **külső tag hozzáadása**, felhasználók vagy csoportok megadása az Azure AD-bérlőben a e-mail-címe. Után kattintson az OK gombra, és zárja be a szerepkör-kezelőt, a szerepkörök és a Tabular Model Explorerben megjelenik. 
 
     ![Szerepkörök és a felhasználók a Tabular Model Explorerben](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Helyezze üzembe az Azure Analysis Services-kiszolgáló.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Adja hozzá, vagy kezelheti a szerepköröket és a felhasználók az ssms-ben
Szerepkörök és a felhasználók hozzáadása egy üzembe helyezett modellű adatbázisához, akkor kapcsolódnia kell a kiszolgáló kiszolgáló-rendszergazdaként vagy a rendszergazda engedélyekkel rendelkező adatbázis-szerepkör már.

1. Az objektum Exporer, kattintson a jobb gombbal **szerepkörök** > **új szerepkör**.

2. A **szerepkör létrehozása**, adja meg a szerepkör nevét és leírását.

3. Válasszon egy engedélyt.
   |Engedély|Leírás|  
   |----------------|-----------------|  
   |**Teljes hozzáférés (rendszergazda)**|Tagjai módosíthatják az eszközmodell sémájának feldolgozásához, és lekérdezheti az összes adatot.| 
   |**Folyamat adatbázis**|Tagok folyamat és a folyamat minden műveleteket is futtathat. Nem módosítható, ha a, és adatokat nem lehet lekérdezni.|  
   |**Olvasás**|A tagok lekérdezheti az adatokat (Sorszűrők alapján), de nem módosíthatja a modellsémát.|  
  
4. Kattintson a **tagsági**, majd adjon meg egy felhasználót vagy csoportot a bérlő Azure ad-ben a e-mail-címe.

     ![Felhasználó hozzáadása](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Ha a szerepkör létrehozásakor olvasási engedéllyel rendelkezik, a Sorszűrők DAX-képlet használatával is hozzáadhat. Kattintson a **Sorszűrők**, válasszon ki egy táblát, és írja be egy DAX-képletet a a **DAX-szűrő** mező. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Szerepkörök és a felhasználók hozzáadása a TMSL-parancsfájl használatával
Az XMLA-ablak az ssms-ben vagy a PowerShell használatával is futtathatja a TMSL-parancsfájl. Használja a [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) parancsot, és a [szerepkörök](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) objektum.

**A példaszkript TMSL**

Ebben a példában egy külső B2B-felhasználó és csoport hozzáadva a SalesBI adatbázis olvasási engedéllyel rendelkező elemző szerepköréhez. A külső felhasználó és a csoport az Azure ad-ben ugyanazt bérlőhöz kell lennie.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Szerepkörök és a felhasználók hozzáadása a PowerShell használatával
A [SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) a modul adja meg a feladat-specifikus adatbázis felügyeleti parancsmagok és az általános célú Invoke-ASCmd parancsmag, amely egy táblázatos modell parancsnyelv (TMSL) lekérdezést vagy parancsfájlt. A következő parancsmagok használhatók, adatbázis-szerepkörök és felhasználók kezeléséhez.
  
|Parancsmag|Leírás|
|------------|-----------------| 
|[Adjon hozzá RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Egy tag hozzáadása egy adatbázis-szerepkörhöz.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Tag eltávolítása egy adatbázis-szerepkörhöz.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Hajtsa végre a TMSL-parancsfájlt.|

## <a name="row-filters"></a>Sorszűrők  
Sorszűrők határozza meg, hogy mely sorokat egy táblában egy adott szerepkör tagjai által kérhető le. Sorszűrők határozzák meg a modellben minden táblához DAX-képletek használatával.  
  
Sorszűrők definiálható csak olvasható, és olvasási szerepkörökhöz tartozó és a folyamat engedélyeket. Alapértelmezés szerint ha egy adott tábla nincs definiálva a sorszűrőt tagok lekérdezheti a tábla összes sorát, ha egy másik tábla keresztszűrés vonatkozik.
  
 Sorszűrők egy DAX-képlet, amely értéket kell adnia egy igaz vagy hamis értéket, hogy lekérdezhetők legyenek, hogy az adott szerepkör tagjai sorok megadásához szükséges. A DAX-képlet nem szereplő sorok nem kérdezhető le. Például a következő sor Customers tábla szűri a kifejezés, *ügyfelek [Ország] = "USA" =*, az értékesítési szerepkör tagjai csak tekintheti meg az ügyfeleknek az USA-ban.  
  
A megadott sort és a hozzá tartozó sorok Sorszűrők vonatkoznak. A tábla több kapcsolattal rendelkezik, a szűrők a kapcsolat aktív biztonsági vonatkoznak. Sorszűrők vannak szakít az egyéb kapcsolódó táblát, például meghatározott sor kiemelik:  
  
|Tábla|DAX-kifejezés|  
|-----------|--------------------|  
|Régió|= Régió [Ország] = "USA"|  
|ProductCategory|= ProductCategory [Name] = "Kerékpárokat"|  
|Tranzakciók|=Transactions[Year]=2016|  
  
 Az eredő hatás tagok sornyi adatot, ahol egy az USA-ban, a termékkategória kerékpárokat, pedig az év 2016 lekérdezheti. Felhasználók nem tudják lekérdezni a tranzakciók kívül az Amerikai Egyesült Államok, tranzakciók, amelyek nem kerékpárokat, vagy a tranzakció nem a 2016-ban, ha azok egy másik szerepkör, amely engedélyezi ezeket az engedélyeket.
  
 Használhatja a szűrő *=FALSE()*, egy egész tábla összes sorát hozzáférését.

## <a name="next-steps"></a>További lépések
  [A kiszolgálók rendszergazdáinak kezelése](analysis-services-server-admins.md)   
  [A PowerShell-lel az Azure Analysis Services kezelése](analysis-services-powershell.md)  
  [Táblázatos modell parancsfájl-kezelési (TMSL) nyelvi referencia](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

