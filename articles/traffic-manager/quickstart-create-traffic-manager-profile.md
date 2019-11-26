---
title: Quickstart:Create a profile for HA of applications - Azure portal - Azure Traffic Manager
description: A rövid útmutató bemutatja, hogyan hozhat létre Traffic Manager-profilokat magas rendelkezésre állású webalkalmazások készítéséhez.
services: traffic-manager
author: asudbring
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: allensu
ms.openlocfilehash: b2163b76dc3a301359cf3474789c5b473f9e4552
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483675"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Quickstart: Create a Traffic Manager profile using the Azure portal

This quickstart describes how to create a Traffic Manager profile that delivers high availability for your web application.

In this quickstart, you'll read about two instances of a web application. Each of them is running in a different Azure region. You'll create a Traffic Manager profile based on [endpoint priority](traffic-manager-routing-methods.md#priority-traffic-routing-method). The profile directs user traffic to the primary site running the web application. Traffic Manager continuously monitors the web application. If the primary site is unavailable, it provides automatic failover to the backup site.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="prerequisites"></a>Előfeltételek

For this quickstart, you'll need two instances of a web application deployed in two different Azure regions (*East US* and *West Europe*). Each will serve as primary and failover endpoints for Traffic Manager.

1. On the upper-left side of the screen, select **Create a resource** > **Web** > **Web App**.

1. In **Create a Web App**, type or select the following values in the **Basics** tab:

   - **Subscription** > **Resource Group**: Select **Create new** and then type **myResourceGroupTM1**.
   - **Instance Details** > **Name**: Type *myWebAppEastUS*.
   - **Instance Details** > **Publish**: Select **Code**.
   - **Instance Details** > **Runtime stack**: Select **ASP.NET V4.7**
   - **Instance Details** > **Operating System**: Select **Windows**.
   - **Instance Details** > **Region**:  Select **East US**.
   - **App Service Plan** > **Windows Plan (East US)** : Select **Create new** and then type **myAppServicePlanEastUS**
   - **App Service Plan** > **Sku and size**: Select **Standard S1**.
   
3. Select the **Monitoring** tab, or select **Next:Monitoring**.  Under **Monitoring**, set **Application Insights** > **Enable Application Insights** to **No**.

4. Select **Review and create**

5. Review the settings, and then click **Create**.  When the Web App successfully deploys, it creates a default web site.

6. Follow the steps to create a second Web App named *myWebAppWestEurope*, with a **Resource Group** name of *myResourceGroupTM2*, a **Region** of *West Europe*, a **App Service Plan** name of **myAppServicePlanWestEurope**, and all the other settings the same as *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Create a Traffic Manager profile that directs user traffic based on endpoint priority.

1. On the upper-left side of the screen, select **Create a resource** > **Networking** > **Traffic Manager profile**.
2. In the **Create Traffic Manager profile**, enter, or select these settings:

    | Beállítás | Value (Díj) |
    | --------| ----- |
    | Név | Enter a unique name for your Traffic Manager profile.|
    | Útválasztási metódus | Select **Priority**.|
    | Előfizetés | Select the subscription you want the traffic manager profile applied to. |
    | Erőforráscsoport | Select *myResourceGroupTM1*.|
    | Földrajzi egység |This setting refers to the location of the resource group. It has no effect on the Traffic Manager profile that will be deployed globally.|

3. Kattintson a **Létrehozás** gombra.

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adja hozzá az *USA keleti régiójában* lévő webhelyt elsődleges végpontként, amelyre az összes felhasználói forgalom át lesz irányítva. Add the website in *West Europe* as a failover endpoint. When the primary endpoint is unavailable, traffic automatically routes to the failover endpoint.

1. In the portal's search bar, enter the Traffic Manager profile name that you created in the preceding section.
2. Select the profile from the search results.
3. In **Traffic Manager profile**, in the **Settings** section, select **Endpoints**, and then select **Add**.
4. Enter, or select, these settings:

    | Beállítás | Value (Díj) |
    | ------- | ------|
    | Type (Típus) | Select **Azure endpoint**. |
    | Név | Enter *myPrimaryEndpoint*. |
    | Célerőforrás típusa | Select **App Service**. |
    | Célerőforrás | Select **Choose an app service** > **East US**. |
    | Prioritás | Válassza az **1** lehetőséget. All traffic goes to this endpoint when it's healthy. |

    ![Screenshot of where you add an endpoint to your Traffic Manager profile.](./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png)

5. Kattintson az **OK** gombra.
6. To create a failover endpoint for your second Azure region, repeat steps 3 and 4 with these settings:

    | Beállítás | Value (Díj) |
    | ------- | ------|
    | Type (Típus) | Select **Azure endpoint**. |
    | Név | Enter *myFailoverEndpoint*. |
    | Célerőforrás típusa | Select **App Service**. |
    | Célerőforrás | Select **Choose an app service** > **West Europe**. |
    | Prioritás | Select **2**. All traffic goes to this failover endpoint if the primary endpoint is unhealthy. |

7. Kattintson az **OK** gombra.

When you're done adding the two endpoints, they're displayed in **Traffic Manager profile**. Notice that their monitoring status is **Online** now.

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

In this section, you'll check the domain name of your Traffic Manager profile. You'll also configure the primary endpoint to be unavailable. Finally, you get to see that the web app is still available. It's because Traffic Manager sends the traffic to the failover endpoint.

### <a name="check-the-dns-name"></a>Check the DNS name

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott **Traffic Manager-profil** nevére.
2. Select the traffic manager profile. The **Overview** appears.
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve.
  
   ![Screenshot of the location of your Traffic Manager DNS name](./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben

1. In a web browser, enter the DNS name of your Traffic Manager profile to view your Web App's default website.

    > [!NOTE]
    > In this quickstart scenario, all requests route to the primary endpoint. It is set to **Priority 1**.

    ![Screenshot of the webpage to confirm availability of Traffic Manager profile](./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png)

2. To view Traffic Manager failover in action, disable your primary site:
    1. In the Traffic Manager Profile page, from the **Overview** section, select **myPrimaryEndpoint**.
    2. In *myPrimaryEndpoint*, select **Disabled** > **Save**.
    3. Close **myPrimaryEndpoint**. Notice that the status is **Disabled** now.
3. Copy the DNS name of your Traffic Manager Profile from the preceding step to view the website in a new web browser session.
4. Verify that the web app is still available.

The primary endpoint isn't available, so you were routed to the failover endpoint.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

When you're done, delete the resource groups, web applications, and all related resources. To do so, select each individual item from your dashboard and select **Delete** at the top of each page.

## <a name="next-steps"></a>Következő lépések

In this quickstart, you created a Traffic Manager profile. It allows you to direct user traffic for high-availability web applications. To learn more about routing traffic, continue to the Traffic Manager tutorials.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
