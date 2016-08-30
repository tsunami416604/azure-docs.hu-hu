<properties
   pageTitle="Az Azure Automation konfigurálása"
   description="Leírja a lépéseket, amelyeket az Azure Automation első használathoz való konfigurálásához kell végrehajtania."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="magoedte;bwren" />

# Az Azure Automation konfigurálása

Ez a cikk az Azure Automation használatának megkezdéséhez szükséges műveleteket írja le.

## Automation-fiókok

Amikor először indítja el az Azure Automationt, legalább egy Automation-fiókot létre kell hoznia. Az Automation-fiókok lehetővé teszik, hogy elkülönítse az Automation-erőforrásait (forgatókönyveket, adategységeket, konfigurációkat) a más Automation-fiókokban tárolt erőforrásoktól. Az Automation-fiókok segítségével külön logikai környezetekre választhatja szét az Automation-erőforrásokat. Használhat például egy fiókot fejlesztéshez, egy másikat pedig az üzemi használatra.

Az Azure-fiókokhoz tartozó Automation-erőforrások egy Azure-régióhoz tartoznak, de az Automation-fiókok képesek bármelyik régióban kezelni az Azure-szolgáltatásokat. A különböző régiókban levő Automation-fiókok létrehozásának fő oka az lehet, ha a házirendeknek adatokra és erőforrásokra van szükségük ahhoz, hogy külön régióra különülhessenek el.

>[AZURE.NOTE] Az Azure portállal létrehozott Automation-fiókok, és a rajtuk tárolt erőforrások nem érhetők el a klasszikus Azure portálról. Ha ezeket fiókokat vagy az erőforrásaikat Windows PowerShellel felügyeli, az Azure Resource Manager modulokat kell használnia. 
>
>A klasszikus Azure portálon létrehozott Automation-fiókokat mindkét portálon, valamint mindkét parancsmagkészlettel is lehet kezelni. A fiók létrehozása után mindegy, hogyan hoz létre és kezel erőforrásokat a fiókon belül. Ha továbbra is a klasszikus Azure portál használatát tervezi, inkább azon hozzon létre Automation-fiókokat az Azure portál helyett.


Egy Automation-fiók felfüggeszthető, ha probléma adódik az Azure-fiókkal, például lejárt tartozás kapcsolódik hozzá. Ebben az esetben, mivel nem férhet hozzá a fiókhoz, a rendszer felfüggeszti az összes futó feladatot, és letiltja az ütemezéseket. Megtekintheti a fiókot, de nem fog látni benne erőforrást. Amint megoldotta a problémát, és a rendszer újra engedélyezi az Automation-fiókot, engedélyeznie kell az ütemezéseit, és újra kell indítania a felfüggesztett forgatókönyveket.


## Az Azure-erőforrások hitelesítésének konfigurálása

Amikor az Azure-erőforrásokat az [Azure-parancsmagokon](http://msdn.microsoft.com/library/azure/jj554330.aspx) keresztül éri el, hitelesítést kell biztosítania az Azure-előfizetéséhez. Az Azure Automation esetében ez az Azure Active Directoryban lévő, az előfizetés rendszergazdájaként konfigurált szervezeti fiókkal hajtható végre. Létrehozhat [hitelesítő adatokat](http://msdn.microsoft.com/library/dn940015.aspx) ehhez a felhasználói fiókhoz, és használhatja a forgatókönyvben a következővel: [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx).

>[AZURE.NOTE] A korábban LiveID néven ismert Microsoft-fiókok nem használhatók az Azure Automation szolgáltatással.

## Új Azure Active Directory-felhasználó létrehozása az Azure-előfizetés kezelésére

1. Jelentkezzen be a klasszikus Azure portálra a kezelni kívánt Azure-előfizetés szolgáltatás-rendszergazdájaként.
2. Az **Active Directory** kiválasztása
3. Válassza ki az Azure-előfizetéshez társított könyvtárnevet. Ha szükséges, ezt a társítást módosíthatja a **Beállítások > Előfizetések > Könyvtár szerkesztése** kiválasztásával.
4. [Hozzon létre egy új Active Directory-felhasználót](http://msdn.microsoft.com/library/azure/hh967632.aspx).  Válassza az **Új felhasználó a szervezetben** lehetőséget a **Felhasználó típusaként**, és ne jelölje be a **Többtényezős hitelesítés engedélyezése** beállítást.
5. Jegyezze fel a felhasználó teljes nevét és ideiglenes jelszavát.
7. Válassza a **Beállítások > Rendszergazdák > Hozzáadás** elemet.
8. Írja be a teljes felhasználónevet, amelyet létrehozott.
9. Válassza ki a felhasználó által kezelendő előfizetést.
10. Lépjen ki az Azure-ból, majd lépjen vissza a fiókkal, amelyet az imént létrehozott. A rendszer meg fogja kérni a felhasználó jelszavának módosítására.
11. Hozzon létre egy új [Azure Automation-hitelesítőeszközt](automation-credentials.md) a létrehozott felhasználói fiókhoz. A **Hitelesítő adatok típusa** legyen **Windows PowerShell-hitelesítő adat**.

## Automation-fiók létrehozása

Az Automation-fiók egy tároló az Azure Automation-erőforrásaihoz. Lehetőséget biztosít a környezetek elkülönítésére vagy a munkamenetek további rendszerezésére. Ha már létrehozott egy Automation-fiókot, ezt a lépést kihagyhatja.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Kattintson az **Új** > **Kezelés** > **Automation-fiók** lehetőségre.

3. Az **Automation-fiók hozzáadása** panelen részletesen konfigurálhatja az Automation-fiókot. 

>[AZURE.NOTE] Ha egy Automation-fiókot az Azure portál segítségével hoznak létre, sem a fiók, sem a hozzá társított erőforrások nem kerülnek vissza a klasszikus Azure portálra. 

Alább látható a konfigurálandó paraméterek listája:

|Paraméter            |Leírás |
|:---|:---|
| Név | Az Automation-fiók neve; egyedi értéknek kell lennie. |
| Erőforráscsoport | Az erőforráscsoportok segítségével egyszerűen megtekintheti és kezelheti a kapcsolódó Azure-erőforrásokat. Az Azure portálon választhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat az Automation-fiókjához, míg a klasszikus Azure portálon az összes Automation-fiók egy alapértelmezett erőforráscsoportba kerül. |
| Előfizetés | Válasszon egy előfizetést az elérhető előfizetések listájáról. |
| Régió | A régió határozza meg, hogy hol tárolja a rendszer az Automation-erőforrásokat a fiókban. A listából bármelyik régiót választhatja, ez nincs hatással a fiók működésére, de a forgatókönyvek végrehajtása gyorsabb lehet, ha a fiókrégió közel esik ahhoz a területhez, ahol az egyéb Azure-erőforrásokat tárolja. |
| Fiókbeállítások | Ez a lehetőség lehetővé teszi annak kiválasztását, hogy melyik erőforrás jöjjön létre az új Automation-fiókban. Ha az **Igen** lehetőséget választja, létrejön egy forgatókönyv-oktatóanyag. |

![Fiók létrehozása](media/automation-configuration/automation-01-create-automation-account.png)

>[AZURE.NOTE] Ha egy klasszikus Azure portálon létrehozott Automation-fiókot az Azure portál használatával [áthelyez egy másik erőforráscsoportba](../resource-group-move-resources.md), az Automation-fiók a továbbiakban nem lesz elérhető a klasszikus Azure portálon.



## Hitelesítés használata egy forgatókönyvben

A hitelesítési adatokat a [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) tevékenység használatával szerezheti meg egy forgatókönyvben, majd az [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) használatával kapcsolhatja össze Azure-előfizetésével. Ha a hitelesítési adatok egy több Azure-előfizetést kezelő rendszergazdához tartoznak, használja a [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) parancsot a megfelelő meghatározásához. Ez látható az alábbi Windows PowerShell-mintában, amely jellemzően a legtöbb Azure Automation forgatókönyv tetején megjelenik.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

A forgatókönyv minden [ellenőrzőpontja](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) után ismételje meg ezeket a sorokat. Ha a forgatókönyvet felfüggesztik, majd később egy másik dolgozón újraindul, ismét végre kell hajtani a hitelesítést.

## Kapcsolódó cikkek
- [Azure Automation: Hitelesítés az Azure-ban az Azure Active Directory segítségével](https://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 



<!--HONumber=jun16_HO2-->


