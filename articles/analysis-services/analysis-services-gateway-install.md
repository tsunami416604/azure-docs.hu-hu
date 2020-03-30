---
title: Helyszíni adatátjáró telepítése az Azure Analysis Services szolgáltatáshoz | Microsoft dokumentumok
description: Megtudhatja, hogyan telepíthet és konfigurálhat helyszíni adatátjárót, hogy az Azure Analysis Services-kiszolgálóról csatlakozzon a helyszíni adatforrásokhoz.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062149"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Helyszíni adatátjáró telepítése és konfigurálása

A helyszíni adatátjáró szükséges, ha egy vagy több Azure Analysis Services-kiszolgáló ugyanabban a régióban csatlakozik a helyszíni adatforrások.  Bár a telepített átjáró megegyezik más szolgáltatások, például a Power BI, a Power Apps és a Logic Apps által használt, az Azure Analysis Services telepítésekor néhány további lépést kell végrehajtania. Ez a telepítési cikk az **Azure Analysis Services-re**vonatkozik. 

Ha többet szeretne tudni arról, hogy az Azure Analysis Services hogyan működik az átjáróval, olvassa el a Csatlakozás helyszíni adatforrásokhoz című [témakört.](analysis-services-gateway.md) A speciális telepítési forgatókönyvekről és általában az átjáróról a [Helyszíni adatátjárók dokumentációjában](/data-integration/gateway/service-gateway-onprem)olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

**Rendszerkövetelmények:**

* .NET-keretrendszer 4.5-ös verziója
* A Windows 8 / Windows Server 2012 R2 (vagy újabb) 64 bites verziója

**Ajánlott:**

* 8 magos processzor
* 8 GB memória
* A Windows 8 / Windows Server 2012 R2 (vagy újabb) 64 bites verziója

**Fontos szempontok:**

* A telepítés során az átjáró Azure-ban történő regisztrálásakor az előfizetés alapértelmezett régiója van kiválasztva. Másik előfizetést és régiót is választhat. Ha egynél több régióban rendelkezik kiszolgálókkal, minden régióhoz telepítenie kell egy átjárót. 
* Az átjáró nem telepíthető tartományvezérlőre.
* Egyetlen számítógépre csak egy átjáró telepíthető.
* Telepítse az átjárót olyan számítógépre, amely bekapcsolva marad, és nem alszik.
* Ne telepítse az átjárót olyan számítógépre, amelycsak vezeték nélküli kapcsolattal rendelkezik a hálózathoz. A teljesítmény csökkenhet.
* Az átjáró telepítésekor a számítógépre bejelentkezett felhasználói fióknak szolgáltatásjogosultságként be kell jelentkeznie. Ha a telepítés befejeződött, a helyszíni adatátjáró szolgáltatás az NT SERVICE\PBIEgwService fiókot használja a szolgáltatásként való bejelentkezéshez. A telepítés befejezése után a telepítés során vagy a Szolgáltatások szolgáltatásban másik fiókot is meg lehet adni. Győződjön meg arról, hogy a csoportházirend-beállítások lehetővé teszik mind azt a fiókot, amelybe a telepítéskor be van jelentkezve, és a választott szolgáltatásfiók szolgáltatásjogosultságként rendelkezik.
* Jelentkezzen be az Azure-ba egy azure-beli fiókkal ugyanahhoz a [bérlőhöz,](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) mint az az előfizetés, amelyben regisztrálja az átjárót. Az Azure B2B (vendég) fiókok nem támogatottak átjáró telepítésekor és regisztrálásakor.
* Ha az adatforrások egy Azure virtuális hálózaton (VNet), konfigurálnia kell a [AlwaysUseGateway](analysis-services-vnet-gateway.md) server tulajdonság.

## <a name="download"></a><a name="download"></a>Letöltés

 [Az átjáró letöltése](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>Telepítés

1. Futtassa a telepítőt.

2. Válassza **a Helyszíni adatátjáró lehetőséget.**

   ![Válassza ezt:](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Jelöljön ki egy helyet, fogadja el a feltételeket, majd kattintson a **Telepítés gombra.**

   ![Telepítés helye és licencfeltételei](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Jelentkezzen be az Azure-ba. A fióknak a bérlő Azure Active Directoryjában kell lennie. Ez a fiók az átjáró rendszergazdája számára használatos. Az Azure B2B (vendég) fiókok nem támogatottak az átjáró telepítéseés regisztrálása során.

   ![Bejelentkezés az Azure-ba](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Ha tartományi fiókkal jelentkezik be, az az Azure AD-ben a szervezeti fiókhoz van rendelve. A szervezeti fiók az átjáró rendszergazdája.

## <a name="register"></a><a name="register"></a>Regisztrálás

Ahhoz, hogy hozzon létre egy átjáró erőforrás t az Azure-ban, regisztrálnia kell a helyi példány telepítve van a Gateway Cloud Service. 

1.  Válassza **az Új átjáró regisztrálása a számítógépen**lehetőséget.

    ![Regisztráljon](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Írja be az átjáró nevét és helyreállítási kulcsát. Alapértelmezés szerint az átjáró az előfizetés alapértelmezett régióját használja. Ha másik régiót kell választania, válassza **a Régió módosítása**lehetőséget.

    > [!IMPORTANT]
    > Mentse a helyreállítási kulcsot biztonságos helyre. A helyreállítási kulcs szükséges az átjáró átvételéhez, áttelepítéséhez vagy visszaállításához. 

   ![Regisztráljon](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Azure-átjáró-erőforrás létrehozása

Miután telepítette és regisztrálta az átjárót, létre kell hoznia egy átjáró-erőforrást az Azure-ban. Jelentkezzen be az Azure-ba ugyanazzal a fiókkal, amelyet az átjáró regisztrálásakor használt.

1. Az Azure Portalon kattintson **az Erőforrás létrehozása**elemre, majd keresse meg a helyszíni **adatátjárót,** majd kattintson a **Létrehozás gombra.**

   ![Átjáró-erőforrás létrehozása](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. A **Kapcsolatátjáró létrehozása**csoportban adja meg az alábbi beállításokat:

   * **Név**: Adja meg az átjáróerőforrás nevét. 

   * **Előfizetés**: Válassza ki az Azure-előfizetést az átjáró-erőforráshoz társítani. 
   
     Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.

   * **Hely**: Válassza ki azt a régiót, ahol regisztrálta az átjárót.

   * **Telepítési név:** Ha az átjáró telepítése még nincs bejelölve, válassza ki a számítógépre telepített és regisztrált átjárót. 

     Ha elkészült, kattintson a **Létrehozás gombra.**

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>Kiszolgálók csatlakoztatása az átjáróerőforráshoz

1. Az Azure Analysis Services-kiszolgáló áttekintésében kattintson **a helyszíni adatátjáró**elemre.

   ![Kiszolgáló csatlakoztatása az átjáróhoz](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. A **Csatlakozáshoz a helyszíni adatátjáró kiválasztása csoportban**jelölje ki az átjáró-erőforrást, majd kattintson a **Kijelölt átjáró csatlakoztatása**elemre.

   ![Kiszolgáló csatlakoztatása átjáróerőforráshoz](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Ha az átjáró nem jelenik meg a listában, a kiszolgáló valószínűleg nem ugyanabban a régióban van, mint az átjáró regisztrálásakor megadott régió.

    Ha a kiszolgáló és az átjáróerőforrás közötti kapcsolat sikeres, az állapot a **Csatlakoztatva**állapot ban jelenik meg.


    ![Kiszolgáló csatlakoztatása az átjáróerőforrás sikeréhez](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Ennyi az egész. Ha portokat kell megnyitnia, vagy bármilyen hibaelhárítást kell végrehajtania, mindenképpen tekintse meg [a helyszíni adatátjárót.](analysis-services-gateway.md)

## <a name="next-steps"></a>További lépések

* [Az Analysis Services felügyelete](analysis-services-manage.md)   
* [Adatok bekéselése az Azure Analysis Servicesszolgáltatásból](analysis-services-connect.md)   
* [Átjáró használata az adatforrásokhoz egy Azure-beli virtuális hálózaton](analysis-services-vnet-gateway.md)
