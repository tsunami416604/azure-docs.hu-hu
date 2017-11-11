---
title: "App Service telepítése kapcsolat nélküli környezetben: Azure verem |} Microsoft Docs"
description: "App Service Azure verem AD FS által védett leválasztott környezetben történő központi telepítéséről részletes útmutatást."
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: cd727b2902dafdb8086ac4ce74db96ca8acf8fe8
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Az App Service erőforrás-szolgáltató felvétele AD FS által védett leválasztott Azure verem környezet

Ez a cikk utasításait követve telepítheti a [App Service erőforrás-szolgáltató](azure-stack-app-service-overview.md) Azure verem környezetbe, amely:
- nem csatlakozik az internethez
- Active Directory összevonási szolgáltatások (AD FS) által védett.

Az App Service erőforrás-szolgáltató hozzáadása a kapcsolat nélküli Azure Alkalmazásveremben üzembe, a legfelső szintű feladatot kell elvégeznie:

1. Fejezze be a [előzetesen szükséges lépések leírását](azure-stack-app-service-before-you-get-started.md) (például a tanúsítványok beszerzése, amely is igénybe vehet néhány nap múlva fogadásához).
2. [A telepítés és a segítő fájlok letöltéséhez és kibontásához](azure-stack-app-service-before-you-get-started.md) géphez csatlakozik az internethez.
3. Hozzon létre egy kapcsolat nélküli csomag.
4. Futtassa a appservice.exe installer fájlt.

## <a name="create-an-offline-installation-package"></a>Egy kapcsolat nélküli csomag létrehozása

App Service-t leválasztott környezetben telepíti, akkor először létre kell hozni egy kapcsolat nélküli csomag olyan gépen, amely kapcsolódik az internethez.

1. Indítsa el a AppService.exe telepítőjét olyan gépen, amely kapcsolódik az internethez.

2. Kattintson a **speciális** > **offline telepítési csomag létrehozása**.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy-offline/image01.png)   

3. Az App Service-telepítő létrehoz egy kapcsolat nélküli telepítési csomagot, és elérési útja. Kattinthat **mappa megnyitása** sikerült megnyitni a mappát a a Fájlkezelőben.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy-offline/image02.png)   

4. A telepítő (AppService.exe) és a kapcsolat nélküli telepítési csomag másolása Azure verem a számítógép.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>A kapcsolat nélküli telepítést veremben Azure App Service

1. A leválasztott Azure verem gazdaszámítógépen azurestack\clouadmin appservice.exe futtató.

2. Kattintson a **speciális** > **offline telepítést**.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy-offline/image03.png)   

3. Keresse meg a helyet, a korábban létrehozott kapcsolat nélküli telepítési csomagot, és kattintson a **következő**.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy-offline/image04.png)   

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-szerződést, és kattintson **következő**.

5. Tekintse át és fogadja el a külső licencfeltételeket majd **következő**.

6. Győződjön meg arról, hogy helyesek-e a App Service felhővédelmi konfigurációs információkat. Azure verem szoftverfejlesztői készlet üzembe helyezése során az alapértelmezett beállításokat használja, ha elfogadhatja az alapértelmezett értékeket itt. Azonban Ha testreszabta a beállítások Azure verem üzembe helyezésekor, szerkesztenie kell a ebben az ablakban megfelelően, hogy az értékek is. Például ha a tartományi utótag mycloud.com használja, a végpontot kell módosítsa management.mycloud.com. Miután meggyőződött róla, hogy az adatait, kattintson a **következő**.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image02.png)

7. A következő lapon:
    1. Kattintson a **Connect** megjelenítő gombra a **Azure verem előfizetések** mezőbe.
        - Azure Active Directory (Azure AD) használata, adja meg az Azure AD rendszergazdai fiókot és az Azure verem telepítésekor megadott jelszót. Kattintson a **bejelentkezés**.
        - Ha Active Directory összevonási szolgáltatások (AD FS) használ, adja meg a rendszergazdai fiókjával. Például: cloudadmin@azurestack.local. Írja be a jelszót, és kattintson a **bejelentkezés**.
    2. Az a **Azure verem előfizetések** jelölje ki az előfizetését.
    3. Az a **Azure verem helyek** válassza ki a helyet, amely megfelel a régióban való telepítése esetén. Válassza például **helyi** Ha a központi telepítése az Azure verem szoftverfejlesztői készlet.
    4. Adjon meg egy **erőforráscsoport-név** az App Service üzembe helyezéshez. Alapértelmezés szerint érték **APPSERVICE\<MOBILE\>**.
    5. Adja meg a **Tárfióknév** , amelyet az App Service-nek a telepítés részeként hozza létre. Alapértelmezés szerint érték **appsvclocalstor**.
    6. Kattintson a **Tovább** gombra.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image03.png)

8. Adja meg a fájlmegosztás adatait, és kattintson a **következő**. A fájlmegosztás címe például kell használnia a teljesen minősített tartománynevét a fájlkiszolgáló \\\appservicefileserver.local.cloudapp.azurestack.external\websites, vagy az IP-címet, például \\\10.0.0.1\websites.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image04.png)

9. A következő lapon:
    1. Az a **identitás Alkalmazásazonosító** mezőbe írja be a GUID-identitás az alkalmazástól.
    2. Az a **identitás alkalmazás tanúsítványfájl** mezőben adja meg (vagy keresse meg a) a tanúsítvány-fájl helyét.
    3. Az a **identitás alkalmazás tanúsítványjelszavas** mezőbe írja be a jelszót a tanúsítványhoz. Ez a jelszó nem azt, amelyik végrehajtott jegyezze fel, ha a parancsfájl a tanúsítványok létrehozásához használt.
    4. Az a **Azure Resource Manager főtanúsítványfájlt** mezőben adja meg (vagy keresse meg a) a tanúsítvány-fájl helyét.
    5. Kattintson a **Tovább** gombra.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image05.png)

10. Az egyes a három tanúsítvány fájl jelölőnégyzetéből, kattintson a **Tallózás** , és keresse meg a megfelelő tanúsítványt fájlt, és adjon meg egy jelszót. Ezek a tanúsítványok azok létrehozott a [hozza létre a szükséges tanúsítványokat lépés](azure-stack-app-service-deploy.md). Kattintson a **következő** után írja be az adatokat.

    | Box | Tanúsítvány fájl neve – példa |
    | --- | --- |
    | **App Service alapértelmezett SSL-tanúsítványfájl** | \_. appservice.local.AzureStack.external.pfx |
    | **Az App Service API SSL tanúsítványfájl** | api.appservice.local.AzureStack.external.pfx |
    | **App Service Publisher SSL tanúsítványfájl** | ftp.appservice.local.AzureStack.external.pfx |

    Ha a tanúsítványok létrehozásakor használt egy másik tartományutótagot, a tanúsítvány fájlnevek ne használjon *helyi. AzureStack.external*. Ehelyett használja az egyéni tartomány adatait.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image06.png)    

11. Adja meg az App Service erőforrás-szolgáltató adatbázisainak üzemeltetésére, és kattintson a server-példány az SQL Server részleteit **következő**. A telepítő ellenőrzi az SQL-kapcsolat tulajdonságai.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image07.png)    

12. Tekintse át a szerepkör példánya és a Termékváltozat-beállítások. Az alapértelmezett beállításokat a rendszer feltölti a példány és az egyes szerepkörökhöz ASDK-telepítés minimális Termékváltozat minimális számát. VCPU és memória követelmények összefoglalása tervezze meg a központi telepítés segítségével valósul meg. Miután elvégezte a beállításokat, kattintson a **következő**.

     > [!NOTE]
     > Az üzemi környezetek útmutatása [Azure App Service kiszolgálói szerepkörök Azure verem Kapacitástervezését](azure-stack-app-service-capacity-planning.md).
     > 
     >

    | Szerepkör | Minimális példányok | Minimális Termékváltozat | Megjegyzések |
    | --- | --- | --- | --- |
    | Tartományvezérlő | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Kezeli, és az App Service-felhő állapotának fenn. |
    | Kezelés | 1 | Standard_A2 - (2 Vcpu, 3584 MB) | Az App Service Azure Resource Manager és az API-végpontok, portál extensions (admin, tenant, Functions portálon) és a szolgáltatás kezeli. A feladatátvétel támogatásához nőtt az ajánlott 2-példányokat. |
    | Közzétevő | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Közzéteszi az FTP és a web deployment a tartalomhoz. |
    | Előtér | 1 | Standard_A1 - (1 vCPU, 1792 MB) | App Service-alkalmazásokra irányuló kérelmek útvonalak. |
    | Megosztott Worker | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Gazdagépek webes API alkalmazásokat és az Azure Functions alkalmazásokat. Érdemes ismételt felvételéhez. Kezelőként határozza meg az ajánlat, és válassza ki bármelyik SKU rétegben. A rétegek rendelkeznie kell legalább egy vCPU. |

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image08.png)    

    > [!NOTE]
    > **Windows Server 2016 Core rendszer nem egy támogatott platform lemezképet használja az Azure App Service Azure veremben**.

13. Az a **Platformlemezképet válasszon** válassza ki a központi telepítés a Windows Server 2016 virtuálisgép-lemezkép azoktól, amelyeket a számítási erőforrás-szolgáltató az App Service-felhő érhető el. Kattintson a **Tovább** gombra.

14. A következő lapon:
     1. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónév és jelszó.
     2. Adja meg a más szerepkörök virtuális gép rendszergazdai felhasználónév és jelszó.
     3. Kattintson a **Tovább** gombra.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image09.png)    

15. Az összefoglalás lapon:
    1. Ellenőrizze a kiválasztott beállítások. Módosításához használja a **előző** gombokra kattintva látogasson el az előző lapokra.
    2. Ha a konfiguráció helyes, jelölje be a jelölőnégyzetet.
    3. A telepítés elindításához kattintson **következő**.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image10.png)    

16. A következő lapon:
    1. A telepítési folyamat előrehaladását. App Service Azure veremben körülbelül percet vesz igénybe 60 központi telepítése az alapértelmezett beállításokat alapján.
    2. Miután a telepítő sikeresen befejeződött, kattintson a **kilépési**.

    ![Az App Service-telepítő](media/azure-stack-app-service-deploy/image11.png)    


## <a name="validate-the-app-service-on-azure-stack-installation"></a>Az App Service, Azure verem telepítés ellenőrzése

1. A verem Azure felügyeleti portálon, a váltson **felügyeleti - App Service**.

2. Az áttekintésben állapota, ellenőrizze, hogy, hogy a **állapot** látható **készen áll az összes szerepkör**.

    ![Az alkalmazásszolgáltatási Management](media/azure-stack-app-service-deploy/image12.png)    


## <a name="test-drive-app-service-on-azure-stack"></a>App Service-meghajtó a Azure verem tesztelése

Miután központilag telepíti, és az App Service erőforrás-szolgáltató regisztrálása, győződjön meg arról, hogy a felhasználók telepíthető-e, webes és API-alkalmazások kipróbálja.

> [!NOTE]
> Az ajánlat, amely rendelkezik a Microsoft.Web névterének a terv létrehozásához szükséges. Majd szeretne egy bérlői előfizetéssel rendelkezik, amelyhez ez az ajánlat számítógépcsoportra fizetett elő. További információkért lásd: [létrehozása ajánlat](azure-stack-create-offer.md) és [terv létrehozása](azure-stack-create-plan.md).
>
Ön *kell* alkalmazásokat, amelyek az App Service használata Azure veremben-bérlői előfizetéssel rendelkezik. Egyetlen funkciója, amelyek a szolgáltatás-rendszergazdák fejezheti be a felügyeleti portálon belül az App Service erőforrás-szolgáltató felügyelethez kapcsolódó. Ezek a képességek közé tartoznak a kapacitást, konfigurálja a telepítési források és munkavégző szinteket, és termékváltozatok hozzáadása.
>
A harmadik technical preview frissítésétől webes API és Azure létrehozásához funkciók alkalmazások, kell a bérlői portál és a bérlői előfizetéssel rendelkezik.

1. Az Azure-verem bérlői portálon kattintson **új** > **Web + mobil** > **webalkalmazás**.

2. Az a **webalkalmazás** panelen írja be a nevét a **webalkalmazás** mezőbe.

3. A **erőforráscsoport**, kattintson a **új**. Adjon meg egy nevet a a **erőforráscsoport** mezőbe.

4. Kattintson az **App Service-csomag/Hely** > **Új létrehozása** lehetőségre.

5. Az a **App Service-csomag** panelen írja be a nevét a **App Service-csomag** mezőbe.

6. Kattintson a **tarifacsomag** > **szabad-megosztott** vagy **megosztott-megosztott** > **válasszon**  >   **OK** > **létrehozása**.

7. Egy perc alatt egy új webalkalmazás csempe jelenik meg az irányítópulton. Kattintson a csempére.

8. A a **Web App** panelen kattintson a **Tallózás** megtekintéséhez az alkalmazás az alapértelmezett webhelyen.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>(Választható) WordPress, DNN vagy Django webhely telepítése

1. Az Azure-verem bérlői portálon kattintson  **+** nyissa meg az Azure piactéren, egy Django-webhely telepítése, és várjon, amíg a művelet sikeresen befejeződött. A Django webes platform jöhet létre fájl rendszer-alapú adatbázist használ. Bármely további erőforrás-szolgáltató SQL vagy MySQL például nem igényel.

2. Amennyiben egy MySQL erőforrás-szolgáltató is telepített, telepíthet egy WordPress-webhely a piactérről. Amikor a rendszer kéri az adatbázis-paraméterek, írja be a felhasználónevet, mint a  *User1@Server1* , a felhasználó nevét és a kívánt kiszolgáló nevét.

3. Ha egy SQL Server erőforrás-szolgáltató is telepített, telepítheti a piactérről DNN webhely. Amikor a rendszer kéri az adatbázis-paraméterek, válasszon egy adatbázist, amely kapcsolódik az erőforrás-szolgáltató SQL Server rendszert futtató számítógép.

## <a name="next-steps"></a>Következő lépések

Is kipróbálhatja más [platformok (PaaS) szolgáltatás](azure-stack-tools-paas-services.md).

- [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md)
- [MySQL-erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
