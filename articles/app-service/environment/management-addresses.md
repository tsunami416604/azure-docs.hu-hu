---
title: Felügyeleti címek
description: Keresse meg az App Service-környezet vezérléséhez használt felügyeleti címeket. Az aszimmetrikus útválasztási problémák elkerülése érdekében konfigurálta őket egy útvonaltáblában.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 11/13/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7d7f97552e8faadee1af928a9ce4e1eea2df476e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687115"
---
# <a name="app-service-environment-management-addresses"></a>App Service-környezet felügyeleti címei

Az App Service Environment (ASE) az Azure Virtuális hálózaton (VNet) futó Azure App Service egyetlen bérlői üzembe helyezése.  Bár az ASE a virtuális hálózaton fut, továbbra is elérhetőnek kell lennie számos dedikált IP-címről, amelyeket az Azure App Service a szolgáltatás kezeléséhez használ.  ASE esetén a felügyeleti forgalom áthalad a felhasználó által vezérelt hálózaton. Ha ez a forgalom blokkolva van, vagy helytelenül irányítják, az ASE felfüggesztésre kerül. Az ASE hálózati függőségeiről a [Hálózati szempontok és az App Service-környezet][networking]című olvasni tudó részletet olvashat. Az ASE-vel kapcsolatos általános információkért kezdje [az App Service-környezet bemutatása][intro]című programmal.

Minden ASEs rendelkezik egy nyilvános VIP-vel, amelybe a menedzsment forgalom kerül. A bejövő felügyeleti forgalom ezekről a címekről érkezik a 454-es és 455-ös portokba az ASE nyilvános VIP-jéhez. Ez a dokumentum felsorolja az App Service forráscímét az ASE-hez irányuló felügyeleti forgalomhoz. Ezek a címek az AppServiceManagement nevű IP-szolgáltatáscímkében is szerepelnek.

Az alábbi címek konfigurálhatók egy útvonaltáblában, hogy elkerüljék a kezelési forgalom aszimmetrikus útválasztási problémákat. Az útvonalak ip-szinten járnak el a forgalomban, és nem ismerik a forgalom irányát, vagy hogy a forgalom egy TCP-válaszüzenet része. Ha a TCP-kérelem válaszcíme eltér a címzett címétől, aszimmetrikus útválasztási probléma merült fel. Az ASE-felügyeleti forgalom aszimmetrikus útválasztási problémáinak elkerülése érdekében győződjön meg arról, hogy a válaszok at ugyanarról a címről küldi vissza, amelyre elküldték őket. Az ASE konfigurálása olyan környezetben való működésre, ahol a kimenő forgalmat a helyszínen küldi, olvassa el [az ASE konfigurálása kényszerített bújtatással című olvasni.][forcedtunnel]

## <a name="list-of-management-addresses"></a>A vezetői címek listája ##

| Régió | Címek |
|--------|-----------|
| Minden nyilvános régió | 13.64.115.203, 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.100.226.236, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.90.240.166, 40.91.126.196, 40.112.242.192, 40.119.4.111, 40.124.47.188, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.80.89, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.43.242.137, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 104.214.49.0, 157.55.176.93, 157.55.208.185, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Hálózati biztonsági csoport konfigurálása

A hálózati biztonsági csoportok esetében nem kell aggódnia az egyes címek miatt vagy a saját konfiguráció karbantartása miatt. Van egy AppServiceManagement nevű IP-szolgáltatáscímke, amely az összes címmel naprakészen van. Ha ezt az IP-szolgáltatáscímkét szeretné használni az NSG-ben, nyissa meg a portált, nyissa meg a hálózati biztonsági csoportok felhasználói felületét, és válassza a Bejövő biztonsági szabályok lehetőséget. Ha rendelkezik egy már meglévő szabályt a bejövő felügyeleti forgalom, szerkesztheti azt. Ha ez az NSG nem az ASE-vel jött létre, vagy ha teljesen új, válassza a **Hozzáadás**lehetőséget. A Forrás legördülő menüben válassza a **Szervizcímke lehetőséget.**  A Forrás szolgáltatás címkéje alatt válassza az **AppServiceManagement lehetőséget.** Állítsa a forrásport \*tartományokat , célról **anyra**, a célport tartományait **454-455-re,** a **TCP**protokollra és az **engedélyezési**műveletre. Ha ön csinál ás a szabály, akkor önnek kell készlet a Prioritás. 

![NSG létrehozása a szolgáltatáscímkével][1]

## <a name="configuring-a-route-table"></a>Útvonaltábla konfigurálása

A felügyeleti címek elhelyezhetők egy útvonaltáblában az internet következő ugrásával, így biztosítható, hogy az összes bejövő felügyeleti forgalom képes legyen ugyanazon az útvonalon keresztül visszatérni. Ezekre az útvonalakra a kényszerített bújtatás konfigurálásakor van szükség. Az útvonaltábla létrehozásához használhatja a portált, a PowerShellt vagy az Azure CLI-t.  Az Azure CLI használatával egy PowerShell-parancssorból az útvonaltábla létrehozásához szükséges parancsok az alábbiakban találhatók. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    $managementAddresses = "13.64.115.203", "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.100.226.236", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.90.240.166", "40.91.126.196", "40.112.242.192", "40.119.4.111", "40.124.47.188", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.80.89", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.14.230", "65.52.172.237", "65.52.193.203", "70.37.57.58", "70.37.89.222", "104.43.242.137", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "104.214.49.0", "157.55.176.93", "157.55.208.185", "191.233.203.64", "191.236.154.88"

    az network route-table create --name $rt --resource-group $rg --location $location
    foreach ($ip in $managementAddresses) {
        az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
    }

Az útvonaltábla létrehozása után be kell állítania az ASE alhálózaton.  

## <a name="get-your-management-addresses-from-api"></a>A felügyeleti címek begyűjtése az API-ból ##

Az alábbi API-hívással felsorolhatja az ASE-nek megfelelő felügyeleti címeket.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Az API egy JSON-dokumentumot ad vissza, amely tartalmazza az ASE összes bejövő címét. A címek listája tartalmazza a felügyeleti címeket, az ASE által használt VIP-t és magát az ASE alhálózati címtartományt.  

Az API-t az [armclient](https://github.com/projectkudu/ARMClient) használatával hívja meg, használja a következő parancsokat, de helyettesítse az előfizetés-azonosítót, az erőforráscsoportot és az ASE-nevet.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
