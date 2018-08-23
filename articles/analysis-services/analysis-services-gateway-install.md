---
title: A helyszíni adatátjáró telepítése |} A Microsoft Docs
description: Ismerje meg, hogyan telepítse és konfigurálja az On-premises data gateway.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 10bcf54da9660da1edcc8358f10289e9678be5cb
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054078"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Telepítse és konfigurálja a helyszíni adatátjáró
Akkor ugyanabban a régióban egy vagy több Azure Analysis Services-kiszolgálót a helyszíni adatforrásokhoz kapcsolódik, egy helyszíni adatátjáróra szükség. Az átjáró kapcsolatos további információkért lásd: [a helyszíni adatátjáró](analysis-services-gateway.md).

## <a name="prerequisites"></a>Előfeltételek
**Rendszerkövetelmények:**

* .NET 4.5-ös keretrendszer
* 64 bites Windows 7 / Windows Server 2008 R2 (vagy újabb)

**Ajánlott:**

* 8 magos CPU
* 8 GB memória
* Windows 2012 R2 (vagy újabb) 64 bites verziója

**Fontos szempontok:**

* Amikor a telepítés során, az átjáró regisztrálása az Azure-ral az alapértelmezett régió az előfizetés van kiválasztva. Választhat egy másik régióban. Ha több régióban a kiszolgálók, telepítenie kell egy átjárót, minden olyan régió esetében. 
* Az átjáró nem telepíthető tartományvezérlőre.
* Csak egy átjáró egyetlen számítógépre telepíthető.
* Telepítse az átjárót olyan számítógépre, amely továbbra is megtalálható, és nem lép alvó állapotba.
* Ne telepítse az átjáró egy a hálózat vezeték nélkül csatlakozó számítógépen. Is csökkenteni kell a teljesítményt.
* Jelentkezzen be Azure-fiókkal ugyanabban az Azure AD-ben [bérlői](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) az előfizetést, az átjáró regisztrálja. Az Azure B2B (vendég) fiókok nem támogatottak, telepítése és a egy átjáró regisztrálása során.
* Ha az adatforrásokat egy Azure virtuális hálózaton (VNet), konfigurálnia kell a [AlwaysUseGateway](analysis-services-vnet-gateway.md) kiszolgáló tulajdonság.
* Az itt ismertetett (egyesített) átjáró nem támogatott az Azure Government, Azure Germany és Azure China szuverén régiók. Használat **dedikált helyszíni átjáró az Azure Analysis Services**, a kiszolgáló telepített **gyors üzembe helyezés** a portálon. 


## <a name="download"></a>Töltse le
 [Töltse le az átjárót](https://aka.ms/azureasgateway)

## <a name="install"></a>Telepítése

1. Futtassa a telepítőt.

2. Válasszon ki egy helyet, fogadja el a feltételeket, és kattintson **telepítése**.

   ![Telepítse a hely és a licenc feltételeit](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Jelentkezzen be az Azure-ba. A fiók a bérlő Azure Active Directoryban kell lennie. Ennek a fióknak az átjáró rendszergazdája szolgál. Az Azure B2B (vendég) fiókok nem támogatottak, telepítése és az átjáró regisztrálása során.

   ![Bejelentkezés az Azure-ba](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Egy tartományi fiókkal jelentkezik be, ha azt lesz leképezve a szervezeti fiókjával az Azure ad-ben. A szervezeti fiók lesz az átjáró rendszergazdájához.

## <a name="register"></a>Regisztráció
Annak érdekében, hogy az átjáró erőforrás létrehozása az Azure-ban, regisztrálnia kell a telepítést az átjáró Felhőszolgáltatása a helyi példány. 

1.  Válassza ki **ezen a számítógépen új átjáró regisztrálása**.

    ![Regisztráljon](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Írja be az átjáró nevét és a helyreállítási kulcsot. Alapértelmezés szerint az átjáró használja az előfizetés alapértelmezett régiójához. Ha kell választania egy másik régióban, válasszon **régió módosítása**.

    > [!IMPORTANT]
    > Mentse a helyreállítási kulcsot biztonságos helyen. A helyreállítási kulcs szükség ahhoz, hogy-átvétel, áttelepítése vagy az átjáró visszaállításához. 

   ![Regisztráljon](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Hozzon létre egy Azure-átjáró-erőforrást
Telepítve van, és az átjáró regisztrálása után kell átjáró erőforrás létrehozása az Azure-előfizetésében. Jelentkezzen be az Azure-ban a fiók is, ha az átjáró regisztrálása.

1. Az Azure Portalon, kattintson a **hozzon létre egy új szolgáltatást** > **vállalati integráció** > **a helyszíni adatátjáró**  >   **Hozzon létre**.

   ![Hozzon létre egy átjáró-erőforrást](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. A **kapcsolódási átjáró létrehozása**, adja meg ezeket a beállításokat:

    * **Név**: Adjon meg egy nevet az átjáró-erőforrás. 

    * **Előfizetés**: válassza ki az Azure-előfizetés társítása az átjáró-erőforrás. 
   
      Az alapértelmezett előfizetést az Azure-fiókkal való bejelentkezéshez használt alapul.

    * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.

    * **Hely**: válassza ki a régiót az átjárót a regisztrált.

    * **Telepítés neve**: Ha az átjáró telepítése még nincs kiválasztva, válassza ki az átjáró regisztrálva. 

    Ha elkészült, kattintson a **létrehozás**.

## <a name="connect-servers"></a>Kiszolgálók csatlakoztatása a gateway-erőforráshoz

1. Kattintson az Azure Analysis Services-kiszolgáló áttekintése **On-Premises Data Gateway**.

   ![Az átjáró-kiszolgáló csatlakoztatása](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. A **az On-Premises Data Gateway való kapcsolódáshoz válasszon**, válassza ki az átjáró-erőforrást, és kattintson **kiválasztott átjáró csatlakoztatása**.

   ![Átjáró erőforrás-kiszolgáló csatlakoztatása](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Ha az átjáró nem szerepel a listán, valószínűleg nem ugyanabban a régióban, mint a terület, meg, ha az átjáró regisztrálása, a server. 

Ennyi az egész. Ha a portok megnyitásához, vagy tegye hibaelhárításra van szüksége, tekintse meg kell [a helyszíni adatátjáró](analysis-services-gateway.md).

## <a name="next-steps"></a>További lépések
* [Analysis Services kezelése](analysis-services-manage.md)   
* [Adatok lekérése az Azure Analysis Services](analysis-services-connect.md)   
* [Az adatforrások egy Azure virtuális hálózati átjáró használata](analysis-services-vnet-gateway.md)
