---
title: Helyszíni adatátjáró telepítése Azure Analysis Serviceshoz | Microsoft Docs
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a helyszíni adatátjárót.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e87a8221a4db4dfab132a91a31a9ba5b5602a3db
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678429"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Helyszíni adatátjáró telepítése és konfigurálása

Egy helyszíni adatátjáróra akkor van szükség, ha egy vagy több Azure Analysis Services-kiszolgáló ugyanabban a régióban csatlakozik a helyszíni adatforrásokhoz.  Míg a telepített átjáró ugyanaz, mint az egyéb szolgáltatások, például a Power BI, a Power apps és a Logic Apps, a Azure Analysis Services telepítésekor néhány további lépést is végre kell hajtania. Ez a telepítési cikk a **Azure Analysis Servicesra**vonatkozik.

Ha többet szeretne megtudni az átjáróról és a Azure Analysis Services használatáról, tekintse [meg a csatlakozás](analysis-services-gateway.md)helyszíni adatforrásokhoz című témakört.

## <a name="prerequisites"></a>Előfeltételek

**Minimális követelmények:**

* .NET 4,5-keretrendszer
* a Windows 7 és a Windows Server 2008 R2 64 bites verziója (vagy újabb)

**Ajánlott**

* 8 magos processzor
* 8 GB memória
* a Windows 2012 R2 64 bites verziója (vagy újabb)

**Fontos szempontok:**

* A telepítés során, amikor az átjárót az Azure-ban regisztrálja, az előfizetéshez tartozó alapértelmezett régió van kiválasztva. Választhat másik régiót is. Ha több régióban is vannak kiszolgálók, mindegyik régióhoz telepítenie kell egy átjárót. 
* Az átjáró nem telepíthető tartományvezérlőre.
* Egyetlen számítógépre egyszerre csak egy átjáró telepíthető.
* Telepítse az átjárót egy olyan számítógépre, amely továbbra is be van kapcsolva, és nem az alvó állapotba lép.
* Ne telepítse az átjárót olyan számítógépre, amely vezeték nélkül csatlakozik a hálózathoz. Csökkentheti a teljesítményt.
* Az átjáró telepítésekor a számítógépre bejelentkezett felhasználói fióknak szolgáltatás-jogosultságként kell bejelentkeznie. Ha a telepítés befejeződött, a helyszíni adatátjáró szolgáltatás az NT SERVICE\PBIEgwService fiókot használja a szolgáltatásként való bejelentkezéshez. A telepítés befejezése után egy másik fiók is megadható a telepítés során vagy a szolgáltatásokban. Győződjön meg arról, Csoportházirend beállítások lehetővé teszik, hogy az Ön által a telepítéskor bejelentkezett fiók és a kiválasztott szolgáltatásfiók szolgáltatásként történő bejelentkezéssel rendelkezzen.
* Jelentkezzen be az Azure-ba egy Azure AD-fiókkal [](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) ugyanahhoz a bérlőhöz, amelyhez az átjárót regisztrálja. Az Azure B2B-(vendég-) fiókok nem támogatottak átjáró telepítésekor és regisztrálása esetén.
* Ha az adatforrások egy Azure Virtual Networkon (VNet) vannak, akkor konfigurálnia kell a [AlwaysUseGateway](analysis-services-vnet-gateway.md) -kiszolgáló tulajdonságát.
* Az itt ismertetett (egyesített) átjáró nem támogatott az Azure Germany-régiókban. Ehelyett használjon **dedikált helyszíni átjárót Azure Analysis Serviceshoz**, amelyet a kiszolgáló **gyorskonfigurálás** a portálon telepítenek. 


## <a name="download"></a>Letöltés

 [Az átjáró letöltése](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Telepítése

1. Futtassa a telepítőt.

2. Válassza **a**helyszíni adatátjáró lehetőséget.

   ![Kiválasztás](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Válasszon ki egy helyet, fogadja el a feltételeket, majd kattintson a **telepítés**gombra.

   ![Telepítési hely és licencfeltételek](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Jelentkezzen be az Azure-ba. A fióknak a bérlő Azure Active Directoryjában kell lennie. Ezt a fiókot használja a rendszer az átjáró rendszergazdájának. Az Azure B2B (Guest) fiókok nem támogatottak az átjáró telepítésekor és regisztrálása során.

   ![Bejelentkezés az Azure-ba](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Ha tartományi fiókkal jelentkezik be, az Azure AD-ben a szervezeti fiókjához van hozzárendelve. A szervezeti fiókját az átjáró rendszergazdája használja.

## <a name="register"></a>Regisztráció

Az Azure-beli átjáró-erőforrások létrehozásához regisztrálnia kell az átjáró Cloud Service-ben telepített helyi példányt. 

1.  Válassza **az új átjáró regisztrálása ezen a számítógépen**lehetőséget.

    ![Regisztrálás](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Adja meg az átjáró nevét és helyreállítási kulcsát. Alapértelmezés szerint az átjáró az előfizetés alapértelmezett régióját használja. Ha másik régiót kell kiválasztania, válassza a **régió módosítása**lehetőséget.

    > [!IMPORTANT]
    > Mentse a helyreállítási kulcsot biztonságos helyen. A helyreállítási kulcs szükséges az átjáró átvételéhez, átmigrálához vagy visszaállításához. 

   ![Regisztrálás](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Azure Gateway-erőforrás létrehozása

Az átjáró telepítése és regisztrálása után létre kell hoznia egy átjáró-erőforrást az Azure-előfizetésében. Jelentkezzen be az Azure-ba ugyanazzal a fiókkal, amelyet az átjáró regisztrálásakor használt.

1. Azure Portal kattintson az **erőforrás létrehozása**elemre, majd keresse **meg a**helyszíni adatátjárót, majd kattintson a **Létrehozás**gombra.

   ![Átjáró erőforrásának létrehozása](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Adja meg a következő beállításokat a **kapcsolatok átjárójának létrehozása**területen:

   * **Név**: Adja meg az átjáró erőforrásának nevét. 

   * **Előfizetés**: Válassza ki az átjáró erőforrásához társítandó Azure-előfizetést. 
   
     Az alapértelmezett előfizetés a bejelentkezéshez használt Azure-fiókon alapul.

   * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.

   * **Hely**: Válassza ki azt a régiót, amelyben az átjárót regisztrálta.

   * **Telepítési név**: Ha az átjáró telepítése még nincs kiválasztva, válassza ki a számítógépen telepített átjárót, és regisztrálja azt. 

     Ha elkészült, kattintson a **létrehozás**.

## <a name="connect-servers"></a>Kiszolgálók összekötése az átjáró erőforrásával

1. A Azure Analysis Services-kiszolgáló áttekintésében kattintson **a**helyszíni adatátjáró elemre.

   ![Kiszolgáló összekötése az átjáróval](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. A **kapcsolódáshoz válasszon egy**helyszíni adatátjárót, válassza ki az átjáró-erőforrást, majd kattintson a **kijelölt átjáró**összekapcsolása elemre.

   ![Kiszolgáló és átjáró erőforrásának összekötése](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Ha az átjáró nem jelenik meg a listában, akkor a kiszolgáló valószínűleg nem ugyanabban a régióban található, mint az átjáró regisztrálásakor megadott régió.

    Ha a kiszolgáló és az átjáró-erőforrás közötti kapcsolat sikeresen befejeződött, azállapot fog megjelenni.


    ![A kiszolgáló és az átjáró erőforrásának összekötése sikeres](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

Ennyi az egész. Ha portok megnyitására vagy hibaelhárításra van szüksége, mindenképpen tekintse [meg a](analysis-services-gateway.md)helyszíni adatátjárót.

## <a name="next-steps"></a>További lépések

* [Az Analysis Services felügyelete](analysis-services-manage.md)   
* [Adatok lekérése Azure Analysis Servicesról](analysis-services-connect.md)   
* [Átjáró használata az adatforrásokhoz egy Azure-beli virtuális hálózaton](analysis-services-vnet-gateway.md)
