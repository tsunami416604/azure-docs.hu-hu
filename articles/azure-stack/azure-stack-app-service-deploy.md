---
title: 'App Service szolgáltatások telepítése: Az Azure verem |} Microsoft Docs'
description: Részletes útmutató a verem Azure App Service telepítése
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 330b8015bdddbbcf27e4325b97e8b734c4d98d12
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Az App Service erőforrás-szolgáltató hozzáadása Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

> [!IMPORTANT]
> A 1802 frissítés alkalmazásához a integrált Azure verem rendszerre, vagy telepítheti a legújabb Azure verem szoftverfejlesztői készlet Azure App Service üzembe helyezése előtt.
>
>

Kezelőként Azure verem felhő biztosíthat a felhasználók a web- és API-alkalmazások létrehozásának képességét. Ehhez fel kell vennie a [App Service erőforrás-szolgáltató](azure-stack-app-service-overview.md) a Azure verem telepítéséhez ebben a cikkben leírtak szerint. Az App Service erőforrás-szolgáltató telepítése után a ajánlatok és tervek megadhat. Felhasználók majd kérhet le a szolgáltatást, és indítsa el az alkalmazások létrehozása.

> [!IMPORTANT]
> A telepítő futtatása, előtt győződjön meg arról, hogy elvégezte-e az útmutató [megkezdése előtt](azure-stack-app-service-before-you-get-started.md).
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Az App Service resource provider telepítőjének futtatása

Az App Service erőforrás-szolgáltató telepítése az Azure-verem környezetbe is igénybe vehet legalább egy órával telepítéséhez választott hány szerepkörpéldányokat függ. A folyamat során a telepítő lesz:

* A blob-tároló létrehozása a megadott verem Azure storage-fiók.
* Hozzon létre egy DNS-zóna és a bejegyzéseket az App Service.
* Az App Service erőforrás-szolgáltató regisztrálása.
* Az App Service gyűjteményelemek regisztrálni.

App Service erőforrás-szolgáltató telepítéséhez kövesse az alábbi lépéseket:

1. Appservice.exe futtassa rendszergazdaként egy olyan számítógépre, az Azure verem Admin Azure Resource felügyeleti végpont érhető el.

2. Kattintson a **App szolgáltatás telepítése vagy frissítése a legújabb verzióra**.

    ![Az App Service-telepítő][1]

3. Tekintse át és fogadja el a Microsoft szoftverlicenc-szerződést, és kattintson **következő**.

4. Tekintse át és fogadja el a külső licencfeltételeket majd **következő**.

5. Győződjön meg arról, hogy helyesek-e a App Service felhővédelmi konfigurációs információkat. Azure verem szoftverfejlesztői készlet üzembe helyezése során az alapértelmezett beállításokat használja, ha elfogadhatja az alapértelmezett értékeket itt. Azonban Ha testreszabta a beállítások, amikor Azure-verem telepítve, vagy az integrált rendszeren telepíti, szerkesztenie kell a ebben az ablakban megfelelően, hogy az értékek is. Például ha a tartományi utótag mycloud.com használja, a Azure verem bérlői Azure Resource Manager-végpontot módosítania kell a felügyeleti. &lt;régió&gt;. mycloud.com. Miután meggyőződött róla, hogy az adatait, kattintson a **következő**.

    ![Az App Service-telepítő][2]

6. A következő lapon:
    1. Kattintson a **Connect** megjelenítő gombra a **Azure verem előfizetések** mezőbe.
        * Azure Active Directory (Azure AD) használata, adja meg az Azure AD rendszergazdai fiókot és Azure verem telepítésekor megadott jelszót. Kattintson a **bejelentkezés**.
        * Ha Active Directory összevonási szolgáltatások (AD FS) használ, adja meg a rendszergazdai fiókjával. Például: cloudadmin@azurestack.local. Írja be a jelszót, és kattintson a **bejelentkezés**.
    2. Az a **Azure verem előfizetések** mezőben válassza a **alapértelmezett szolgáltató előfizetés**.
    3. Az a **Azure verem helyek** válassza ki a helyet, amely megfelel a régióban való telepítése esetén. Válassza például **helyi** Ha a központi telepítése az Azure verem szoftverfejlesztői készlet.

    ![Az App Service-telepítő][3]

4. Most már rendelkezik konfigurált végig a lépéseken létrehozni meglévő virtuális hálózatban telepíteni [Itt](azure-stack-app-service-before-you-get-started.md#virtual-network), vagy engedélyezheti az App Service telepítő virtuális hálózat és a társított alhálózatok létrehozásához.
    1. Válassza ki **VNet létrehozása az alapértelmezett beállításokkal**, fogadja el az alapértelmezett beállításokat, majd kattintson **következő**, vagy;
    2. Válassza ki **használja a meglévő hálózatok és alhálózatok**.
        1. Válassza ki a **erőforráscsoport** , amely tartalmazza a virtuális hálózat;
        2. Válassza ki a megfelelő **virtuális hálózati** kívánja üzembe helyezés; neve
        3. Válassza ki a megfelelő **alhálózati** az egyes a szükséges szerepkör-alhálózatok;
        4. Kattintson a **Tovább** gombra

    ![Az App Service-telepítő][4]

7. Adja meg a fájlmegosztás adatait, és kattintson a **következő**. A fájlmegosztás címét a teljesen minősített tartománynév vagy IP-címet a fájlkiszolgáló kell használnia. Például \\\appservicefileserver.local.cloudapp.azurestack.external\websites, vagy \\\10.0.0.1\websites.

   > [!NOTE]
   > A telepítő megpróbálja használt kapcsolatok tesztelése a fájlmegosztáshoz való továbblépéshez.  Azonban ha úgy döntött, és a meglévő virtuális hálózat telepíthető, a telepítő nem lehet csatlakozni a fájlmegosztás és a figyelmeztetés nem jelenik megkérdezi, hogy a folytatáshoz.  Ellenőrizze a fájlmegosztási adatokat, és továbbra is, ha azok helyességét.
   >
   >

   ![Az App Service-telepítő][7]

8. A következő lapon:
    1. Az a **identitás Alkalmazásazonosító** adja meg a globálisan egyedi Azonosítót (az Azure AD) identitás használata az alkalmazás.
    2. Az a **identitás alkalmazás tanúsítványfájl** mezőben adja meg (vagy keresse meg a) a tanúsítvány-fájl helyét.
    3. Az a **identitás alkalmazás tanúsítványjelszavas** mezőbe írja be a jelszót a tanúsítványhoz. Ez a jelszó nem azt, amelyik végrehajtott jegyezze fel, ha a parancsfájl a tanúsítványok létrehozásához használt.
    4. Az a **Azure Resource Manager főtanúsítványfájlt** mezőben adja meg (vagy keresse meg a) a tanúsítvány-fájl helyét.
    5. Kattintson a **Tovább** gombra.

    ![Az App Service-telepítő][9]

9. Az egyes a három tanúsítvány fájl jelölőnégyzetéből, kattintson a **Tallózás** , és keresse meg a megfelelő tanúsítványfájlt. Minden tanúsítványt meg kell adnia a jelszót. Ezek a tanúsítványok azok létrehozott a [hozza létre a szükséges tanúsítványokat lépés](azure-stack-app-service-before-you-get-started.md#get-certificates). Kattintson a **következő** után írja be az adatokat.

    | Box | Tanúsítvány fájl neve – példa |
    | --- | --- |
    | **App Service alapértelmezett SSL-tanúsítványfájl** | \_.appservice.local.AzureStack.external.pfx |
    | **Az App Service API SSL tanúsítványfájl** | api.appservice.local.AzureStack.external.pfx |
    | **App Service Publisher SSL tanúsítványfájl** | ftp.appservice.local.AzureStack.external.pfx |

    Ha a tanúsítványok létrehozásakor használt egy másik tartományutótagot, a tanúsítvány fájlnevek ne használjon *helyi. AzureStack.external*. Ehelyett használja az egyéni tartomány adatait.

    ![Az App Service-telepítő][10]

10. Adja meg az App Service erőforrás-szolgáltató adatbázisainak üzemeltetésére, és kattintson a server-példány az SQL Server részleteit **következő**. A telepítő ellenőrzi az SQL-kapcsolat tulajdonságai.

    > [!NOTE]
    > A telepítő megpróbálja tesztelése az SQl Server, a folytatás előtt.  Azonban ha úgy döntött, központi telepítéséhez a meglévő virtuális hálózat, a telepítő nem lehet csatlakozni az SQL Server és a figyelmeztetés nem jelenik megkérdezi, hogy a folytatáshoz.  Ellenőrizze az SQL Server-adatokat, és továbbra is, ha azok helyességét.
    >
    >

    ![Az App Service-telepítő][11]

11. Tekintse át a szerepkör példánya és a Termékváltozat-beállítások. Az alapértelmezett példány és az egyes szerepkörökhöz ASDK-telepítés minimális Termékváltozat minimális számú feltöltéséhez. VCPU és memória követelmények összefoglalása tervezze meg a központi telepítés segítségével valósul meg. Miután elvégezte a beállításokat, kattintson a **következő**.

    > [!NOTE]
    > Az üzemi környezetek útmutatása [Azure App Service kiszolgálói szerepkörök Azure verem Kapacitástervezését](azure-stack-app-service-capacity-planning.md).
    >
    >

    | Szerepkör | Minimális példányok | Minimális Termékváltozat | Megjegyzések |
    | --- | --- | --- | --- |
    | Vezérlő | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Kezeli, és az App Service-felhő állapotának fenn. |
    | Kezelés | 1 | Standard_A2 - (2 Vcpu, 3584 MB) | Az App Service Azure Resource Manager és az API-végpontok, portál extensions (admin, tenant, Functions portálon) és a szolgáltatás kezeli. A feladatátvétel támogatásához nőtt az ajánlott 2-példányokat. |
    | Közzétevő | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Közzéteszi az FTP és a web deployment a tartalomhoz. |
    | Előtér | 1 | Standard_A1 - (1 vCPU, 1792 MB) | App Service-alkalmazásokra irányuló kérelmek útvonalak. |
    | Megosztott Worker | 1 | Standard_A1 - (1 vCPU, 1792 MB) | Gazdagépek webes API alkalmazásokat és az Azure Functions alkalmazásokat. Érdemes ismételt felvételéhez. Kezelőként határozza meg az ajánlat, és válassza ki bármelyik SKU rétegben. A rétegek rendelkeznie kell legalább egy vCPU. |

    ![Az App Service-telepítő][13]

    > [!NOTE]
    > **Windows Server 2016 Core nincs támogatott platformlemezkép való használathoz az Azure App Service Azure veremben.  Ne használja az üzemi környezetek értékelési képek.**

12. Az a **Platformlemezképet válasszon** a központi telepítés a Windows Server 2016 virtuálisgép-lemezkép érhető el az App Service-felhő számítási erőforrás-szolgáltató képek közül választhat. Kattintson a **Tovább** gombra.

13. A következő lapon:
     1. Adja meg a feldolgozói szerepkör virtuális gép rendszergazdai felhasználónév és jelszó.
     2. Adja meg a más szerepkörök virtuális gép rendszergazdai felhasználónév és jelszó.
     3. Kattintson a **Tovább** gombra.

    ![Az App Service-telepítő][15]    

14. Az összefoglalás lapon:
    1. Ellenőrizze a kiválasztott beállítások. Módosításához használja a **előző** gombokra kattintva látogasson el az előző lapokra.
    2. Ha a konfiguráció helyes, jelölje be a jelölőnégyzetet.
    3. A telepítés elindításához kattintson **következő**.

    ![Az App Service-telepítő][16]

15. A következő lapon:
    1. A telepítési folyamat előrehaladását. App Service Azure veremben körülbelül percet vesz igénybe 60 központi telepítése az alapértelmezett beállításokat alapján.
    2. Miután a telepítő sikeresen befejeződött, kattintson a **kilépési**.

    ![Az App Service-telepítő][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Az App Service, Azure verem telepítés ellenőrzése

1. A verem Azure felügyeleti portálon, a váltson **felügyeleti - App Service**.

2. Az áttekintésben állapota, ellenőrizze, hogy, hogy a **állapot** látható **készen áll az összes szerepkör**.

    ![Az alkalmazásszolgáltatási Management](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> Ha úgy dönt, hogy az üzembe helyezés meglévő virtuális hálózat és a fájlkiszolgáló a conenct egy belső IP-címet, hozzá kell adnia egy kimenő biztonsági szabály engedélyezése az SMB adatforgalmát. a munkavégző és a fájlkiszolgáló között.  Ehhez nyissa meg a felügyeleti portál WorkersNsg, és a következő tulajdonságokkal kimenő biztonsági szabály felvétele:
> * Forrás: bármely
> * Forrás-porttartomány: *
> * Cél: IP-címek
> * Cél IP-címtartomány: IP-címtartományra vonatkozó a fájlkiszolgáló
> * Célporttartomány: 445-ös
> * Protokoll: TCP
> * Művelet: engedélyezése
> * Prioritás: 700
> * Name: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>App Service-meghajtó a Azure verem tesztelése

Miután központilag telepíti, és az App Service erőforrás-szolgáltató regisztrálása, győződjön meg arról, hogy a felhasználók telepíthető-e, webes és API-alkalmazások kipróbálja.

> [!NOTE]
> Az ajánlat, amely rendelkezik a Microsoft.Web névterének a terv létrehozásához szükséges. Majd szeretne egy bérlői előfizetéssel rendelkezik, amelyhez ez az ajánlat számítógépcsoportra fizetett elő. További információkért lásd: [létrehozása ajánlat](azure-stack-create-offer.md) és [terv létrehozása](azure-stack-create-plan.md).
>
Ön *kell* alkalmazásokat, amelyek az App Service használata Azure veremben-bérlői előfizetéssel rendelkezik. Egyetlen funkciója, amelyek a szolgáltatás-rendszergazdák fejezheti be a felügyeleti portálon belül az App Service erőforrás-szolgáltató felügyelethez kapcsolódó. Ezek a képességek közé tartoznak a kapacitást, konfigurálja a telepítési források és munkavégző szinteket, és termékváltozatok hozzáadása.
>
Webes API és Azure létrehozásához funkciókkal, alkalmazások, kell a bérlői portál és a bérlői előfizetéssel rendelkezik.

1. Az Azure-verem bérlői portálon kattintson **új** > **Web + mobil** > **webalkalmazás**.

2. Az a **webalkalmazás** panelen írja be a nevét a **webalkalmazás** mezőbe.

3. A **erőforráscsoport**, kattintson a **új**. Adjon meg egy nevet a a **erőforráscsoport** mezőbe.

4. Kattintson az **App Service-csomag/Hely** > **Új létrehozása** lehetőségre.

5. Az a **App Service-csomag** panelen írja be a nevét a **App Service-csomag** mezőbe.

6. Kattintson a **tarifacsomag** > **szabad-megosztott** vagy **megosztott-megosztott** > **válasszon**  >   **OK** > **létrehozása**.

7. Egy perc alatt egy új webalkalmazás csempe jelenik meg az irányítópulton. Kattintson a csempére.

8. A a **Web App** panelen kattintson a **Tallózás** megtekintéséhez az alkalmazás az alapértelmezett webhelyen.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>(Választható) WordPress, DNN vagy Django webhely telepítése

1. Az Azure-verem bérlői portálon kattintson **+**nyissa meg az Azure piactéren, egy Django-webhely telepítése, és várjon, amíg a művelet sikeresen befejeződött. A Django webes platform jöhet létre fájl rendszer-alapú adatbázist használ. Bármely további erőforrás-szolgáltató SQL vagy MySQL például nem igényel.

2. Amennyiben egy MySQL erőforrás-szolgáltató is telepített, telepíthet egy WordPress-webhely a piactérről. Amikor a rendszer kéri az adatbázis-paraméterek, írja be a felhasználónevet, mint a *User1@Server1*, a felhasználó nevét és a kívánt kiszolgáló nevét.

3. Ha egy SQL Server erőforrás-szolgáltató is telepített, telepítheti a piactérről DNN webhely. Amikor a rendszer kéri az adatbázis-paraméterek, válasszon egy adatbázist, amely kapcsolódik az erőforrás-szolgáltató SQL Server rendszert futtató számítógép.

## <a name="next-steps"></a>További lépések

Is kipróbálhatja más [platformok (PaaS) szolgáltatás](azure-stack-tools-paas-services.md).

- [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md)
- [MySQL-erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
