---
title: Adatbázis-szerepkörök és a felhasználók az Azure Analysis Services kezelése |} Microsoft Docs
description: 'Útmutató: az adatbázis-szerepkörök és az Analysis Services-kiszolgálóhoz, az Azure-ban a felhasználók kezelése.'
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3c35fb1ee70544b8b01bbadaf72ee38145179b27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="manage-database-roles-and-users"></a>Adatbázis-szerepkörök és a felhasználók kezelése

A modell adatbázis szinten minden felhasználó egy szerepkörhöz kell tartoznia. Szerepkörök definiálása a modelladatbázis adott jogosultságokkal rendelkező felhasználók. Bármely felhasználónak vagy biztonsági csoportot hozzáadni a szerepkörhöz ugyanazt az előfizetést, mint a kiszolgáló Azure AD-bérlő fiókkal kell rendelkeznie.

Szerepkörök definiálása hogyan eltér attól függően, hogy a használt eszköz, azonban a hatás azonos.

Szerepkör-engedélyek a következők:
*  **Rendszergazda** -felhasználók az adatbázis teljes körű engedélyekkel rendelkezik. Adatbázis-szerepkörök rendszergazdai jogosultságokkal rendelkező rendszergazdáinak eltérnek.
*  **Folyamat** -felhasználók csatlakozhat és az adatbázis folyamat műveleteket, és modell adatbázis elemzéséhez.
*  **Olvasási** -felhasználók ügyfélalkalmazás segítségével csatlakozhat, és elemezni a modell adatbázis adatai.

Amikor létrehoz egy táblázatos modell projektet, szerepköröket hozhat létre, és SSDT szerepkör-kezelővel ezeket a szerepköröket felhasználók vagy csoportok hozzáadása. Egy kiszolgáló telepítésekor használhatja SSMS, [Analysis Services PowerShell-parancsmagok](https://msdn.microsoft.com/library/hh758425.aspx), vagy [táblázatos modell Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) hozzáadása vagy eltávolítása a szerepkörök és a felhasználói tagjai.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Adja hozzá vagy szerepkörök és az SSDT felhasználók kezelése  
  
1.  Az SSDT > **táblázatos modell Explorer**, kattintson a jobb gombbal **szerepkörök**.  
  
2.  A **Szerepkörkezelőben** kattintson az **Új** lehetőségre.  
  
3.  Írja be a szerepkör nevét.  
  
     Alapértelmezés szerint az alapértelmezett szerepkör neve Növekményesen számozott minden új szerepkörhöz. Javasoljuk, adjon meg egy nevet, amely egyértelműen azonosítja a tag típusa, például a pénzügyi menedzserek vagy az emberi erőforrások szakemberei.  
  
4.  Válassza ki a következő jogosultságok egyikével rendelkeznek:  
  
    |Engedély|Leírás|  
    |----------------|-----------------|  
    |**Egyik sem**|A tagok nem módosítható a modellsémát, és nem tudja lekérdezni az adatokat.|  
    |**Olvasás**|Tagok lekérdezheti adatokat (sorszűrőket alapján), de nem módosíthatja a modellsémát.|  
    |**Olvasás és a folyamat**|Tagok lekérdezheti adatokat (a sorszintű szűrők) és futtatási folyamat és a folyamat minden műveleteket, de nem módosítható a modellsémát.|  
    |**Folyamat**|Tagok folyamat és a folyamat minden műveletek is futtatható. A modell sémája nem módosítható és nem tudja lekérdezni az adatokat.|  
    |**Rendszergazda**|Tagok modellsémát módosíthatja, és minden adat lekérdezése.|   
  
5.  Ha a szerepkör létrehozása rendelkezik olvasási vagy olvasási és a folyamat engedélyt adhat hozzá sorszűrőket DAX-képlet használatával. Kattintson a **sorszűrőket** lapra, majd válasszon egy táblázatot, majd kattintson a **DAX-szűrő** mezőben, és írja be egy DAX-képletet.
  
6.  Kattintson a **tagok** > **vegye fel a külső**.  
  
8.  A **külső tag hozzáadása**, adjon meg felhasználókat vagy csoportokat az Azure AD-bérlőben e-mail címet. Kattintson az OK gombra, és zárja be a szerepkör-kezelőt, szerepkörök és a szerepkör tagjai jelennek meg a táblázatos modell Explorer. 
 
     ![Szerepkörök és a táblázatos modell Explorer felhasználók](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Telepítse az Azure Analysis Services-kiszolgálóhoz.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Adja hozzá vagy szerepkörök és a szolgáltatáshoz az ssms felhasználók kezelése
Szerepkörök és felhasználók felvétele egy telepített modellű adatbázisnál, akkor kapcsolódnia kell a kiszolgáló kiszolgálói rendszergazdaként vagy a rendszergazdai jogosultságokkal rendelkező adatbázis-szerepkör már.

1. Az objektum Exporer, kattintson a jobb gombbal **szerepkörök** > **új szerepkör**.

2. A **szerepkör létrehozása**, írja be a szerepkör nevét és leírását.

3. Jelöljön ki egy engedélyt.
   |Engedély|Leírás|  
   |----------------|-----------------|  
   |**Teljes hozzáférés (rendszergazda)**|Tagjai módosíthatják a modellsémát feldolgozásához, és lekérdezheti az összes adatot.| 
   |**Folyamat adatbázis**|Tagok folyamat és a folyamat minden műveletek is futtatható. A modell sémája nem módosítható és nem tudja lekérdezni az adatokat.|  
   |**Olvasás**|Tagok lekérdezheti adatokat (sorszűrőket alapján), de nem módosíthatja a modellsémát.|  
  
4. Kattintson a **tagsági**, majd írjon be egy felhasználót vagy csoportot az Azure AD bérlőhöz e-mail cím alapján.

     ![Felhasználó hozzáadása](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Ha a létrehozni kívánt szerepkör olvasási engedéllyel rendelkezik, egy DAX-képlet használatával adhat hozzá a sor szűrők. Kattintson a **sorszűrőket**, válasszon ki egy táblát, és írja be a DAX-képlet a **DAX-szűrő** mező. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Szerepkörök és felhasználók hozzáadása TMSL parancsfájl használatával
Az XMLA-ablakban szolgáltatáshoz az ssms vagy a PowerShell használatával TMSL parancsfájlt is futtathatja. Használja a [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) parancs és a [szerepkörök](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) objektum.

**Mintaparancsfájl TMSL**

Ez a példa egy B2B külső felhasználó vagy csoport kerülnek a SalesBI adatbázis olvasási engedéllyel rendelkező elemző szerepkörre. A külső felhasználó és a csoport az Azure AD ugyanannak a bérlőnek kell lennie.

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
A [SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) modul adja meg a tevékenység-specifikus adatbázis parancsmagokat és az általános célú Invoke-ASCmd parancsmag, amely egy táblázatos modell Scripting Language (TMSL) lekérdezés vagy parancsfájl fogadja. A következő parancsmagok használhatók adatbázis-szerepkörök és felhasználók kezeléséhez.
  
|Parancsmag|Leírás|
|------------|-----------------| 
|[Adja hozzá RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Tag hozzáadása egy adatbázis-szerepkör.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Tag eltávolítása egy adatbázis-szerepkör.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Hajtsa végre a TMSL parancsfájlt.|

## <a name="row-filters"></a>Sorszűrőket  
Sorszűrőket határozza meg, mely egy tábla sorainak lekérdezhetők, egy adott szerepkör tagjai. Sorszűrőket DAX-képlet használatával definiáltak a modellben minden táblához.  
  
Sorszűrőket csak rendelkező olvasási és olvasási szerepkörökhöz adható meg és folyamat-engedélyeket. Alapértelmezés szerint ha egy adott tábla nincs definiálva a Sorszűrő tagok lekérdezheti a tábla összes sorát kivéve, ha egy másik táblából keresztszűrési vonatkozik.
  
 Sorszűrőket kell a DAX-képlet, amely ki kell értékelnie minden igaz vagy hamis értéket, adja meg a sorok, amelyekre ez a szerepkör tagjai által kérdezhetők le. A DAX-képlet nem szereplő sorok nem kérdezhető le. Például a következő sort az ügyfelek tábla szűrők kifejezését *ügyfelek [Ország] = "USA" =*, az értékesítési szerepkör tagjai csak az USA ügyfelek tekintheti meg.  
  
A megadott sorok és a kapcsolódó sorok sorszűrőket alkalmazni. Egy táblázat több olyan kapcsolattal rendelkezik, szűrők a kapcsolat aktív biztonsági vonatkoznak. Sorszűrőket az egyéb kapcsolódó tábla, például meghatározott sor szûrõkkel vannak átfedő:  
  
|Tábla|DAX-kifejezés|  
|-----------|--------------------|  
|Régió|=Region[Country]=”USA”|  
|ProductCategory|= ProductCategory [Name] = "Kerékpárt"|  
|Tranzakciók|=Transactions[Year]=2016|  
  
 A nettó hatása az tagok sornyi adatot, ha az ügyfél az USA, a termék kategóriáját kerékpárt, és az év 2016 lekérdezheti. Felhasználók kívül az USA tranzakciók, amelyek nincsenek kerékpárt vagy tranzakciók nem 2016 kivéve, ha egy másik szerepkör, amely engedélyt ad a tranzakciók nem tudja lekérdezni.
  
 A szűrővel *=FALSE()*, hogy megtagadja a hozzáférést a teljes táblázat összes sorát.

## <a name="next-steps"></a>További lépések
  [Kiszolgáló-rendszergazdák kezelése](analysis-services-server-admins.md)   
  [A PowerShell segítségével az Azure Analysis Services kezelése](analysis-services-powershell.md)  
  [Táblázatos modell Scripting (TMSL) nyelvi referencia](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

