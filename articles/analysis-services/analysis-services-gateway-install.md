---
title: A helyszíni adatátjáró telepítése |} Microsoft Docs
description: Megtudhatja, hogyan telepítse és konfigurálja az On-premises adatátjáró.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/03/2018
ms.author: owend
ms.openlocfilehash: b57ee7d9617f8bf2e54122ac37f351ae0cf9db26
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Telepítse és konfigurálja a helyszíni adatátjáró
Egy a helyszíni adatok átjáróra szükség, ha egy vagy több Azure Analysis Services kiszolgáló ugyanabban a régióban kapcsolódnak a helyszíni adatforrások. Az átjáró kapcsolatos további információkért lásd: [helyszíni adatátjáró](analysis-services-gateway.md).

## <a name="prerequisites"></a>Előfeltételek
**Minimumkövetelmények:**

* .NET 4.5 keretrendszer
* 64 bites Windows 7 vagy Windows Server 2008 R2 (vagy újabb)

**Ajánlott:**

* 8 mag Processzor
* 8 GB memória
* 64 bites Windows 2012 R2 (vagy újabb)

**Fontos tudnivalók találhatók:**

* A telepítés során, ha az átjáró regisztrálása az Azure-ral az alapértelmezett terület előfizetési van kiválasztva. Lehetősége van egy másik régióban. Ha egynél több régióban kiszolgálóval rendelkezik, telepítenie kell egy átjáró mindegyik régióhoz. 
* Az átjáró nem telepíthető tartományvezérlőre.
* Csak egy átjáró telepíthető egy számítógépen.
* Az átjáró telepítése egy számítógépre, továbbra is megtalálható, és nem alvó állapotba lép.
* Az átjáró nem telepíthető olyan számítógépre, vezeték nélküli kapcsolódik a hálózathoz. Teljesítmény is lehet csökken.
* Jelentkezzen be Azure-fiókkal azonos az Azure AD-ben [bérlői](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) az átjáró regisztrál, az előfizetés. Az Azure B2B (vendég) fiókok nem támogatottak, telepítése és az átjáró regisztrálása során.
* Az itt leírt (egyesített) átjáró Azure Government (Fairfax) nem támogatott. A dedikált helyszíni átjáró használatához Azure Analysis Services. A dedikált átjáró nem támogatja a további szolgáltatásokat, mint a Power BI és a kiemelt alkalmazások. Dedikált átjáró telepítve van a kiszolgálókról, gyors üzembe helyezés, a portálon.


## <a name="download"></a>Letöltése
 [Az átjáró letöltése](https://aka.ms/azureasgateway)

## <a name="install"></a>Telepítése

1. Futtassa a telepítőt.

2. Jelöljön ki egy helyet, fogadja el a feltételeket, és kattintson **telepítése**.

   ![Telepítse a helyét és a licencszerződés feltételeit](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Jelentkezzen be az Azure-ba. A fióknak kell lennie a bérlő Azure Active Directoryban. Ez a fiók szolgál az átjáró rendszergazdájához. Az Azure B2B (vendég) fiókok nem támogatottak, telepítése és az átjáró regisztrálása során.

   ![Bejelentkezés az Azure-ba](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Ha jelentkezik be egy olyan tartományi fiók, van leképezve a szervezeti fiók az Azure ad-ben. A szervezeti fiók lesz az átjáró rendszergazdájához.

## <a name="register"></a>Register
Ahhoz, hogy hozzon létre egy átjáró erőforrást az Azure-ban, regisztrálnia kell az átjáró Felhőszolgáltatáshoz telepítette a helyi példány. 

1.  Válassza ki **ezen a számítógépen egy új átjáró regisztrálása**.

    ![Regisztráljon](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Írja be az átjáró nevét és a helyreállítási kulcsot. Az átjáró alapértelmezés szerint az előfizetés alapértelmezett régió használja. Ha egy másik régiót van szüksége, válassza ki a **régió módosítása**.

    > [!IMPORTANT]
    > A helyreállítási kulcs menthető egy biztonságos helyen. A helyreállítási kulcs szükséges sorrendben történő felvásárlási, telepítse át, vagy állítsa vissza egy átjáró. 

   ![Regisztráljon](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Hozzon létre egy Azure átjáró erőforrást
Miután telepíteni és regisztrálni az átjárót, szüksége átjáró erőforrás létrehozása az Azure-előfizetéshez. Jelentkezzen be Azure ugyanazt a fiókot, ha regisztrálja az átjárót használja.

1. Az Azure portálon kattintson **hozzon létre egy új** > **vállalati integrációs** > **helyszíni adatátjáró** > **létrehozása**.

   ![Hozzon létre egy átjáró erőforrást](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. A **kapcsolat átjáró létrehozása**, adja meg ezeket a beállításokat:

    * **Név**: Adjon meg egy nevet az átjáró-erőforráshoz. 

    * **Előfizetés**: válassza ki az Azure-előfizetés társítása az átjáró-erőforráshoz. 
   
      Az alapértelmezett előfizetés bejelentkezéshez használt Azure-fiók alapul.

    * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.

    * **Hely**: regisztrálta az átjáró, a régiót.

    * **Telepítési neve**: Ha nincs kiválasztva, akkor az átjáró telepítésének, jelölje be az átjáró regisztrálása. 

    Amikor elkészült, kattintson a **létrehozása**.

## <a name="connect-servers"></a>Csatlakoztassa a kiszolgálókat az átjáró-erőforráshoz

1. Kattintson az Azure Analysis Services-kiszolgáló áttekintése **helyszíni adatátjáró**.

   ![Csatlakoztassa a kiszolgálót átjáró](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. A **válasszon egy helyszíni Adatátjárót csatlakozni**, jelölje be az átjáró-erőforráshoz, és kattintson a **csatlakozás a kiválasztott átjáróeszközön**.

   ![Csatlakoztassa a kiszolgálót átjáró erőforrás](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Ha az átjáró nem jelenik meg a listában, a kiszolgáló valószínű nem ugyanabban a régióban, mint a terület a megadott az átjáró regisztrálásakor. 

Ennyi az egész. Ha a portok megnyitásához, vagy tegye a hibaelhárításra van szüksége, ügyeljen arra, hogy tekintse meg [helyszíni adatátjáró](analysis-services-gateway.md).

## <a name="next-steps"></a>További lépések
* [Analysis Services kezelése](analysis-services-manage.md)   
* [Adatok beolvasása az Azure Analysis Services](analysis-services-connect.md)
