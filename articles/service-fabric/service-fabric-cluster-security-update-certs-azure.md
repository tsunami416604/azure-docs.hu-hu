---
title: Tanúsítványok az Azure Service Fabric-fürt kezelése |} A Microsoft Docs
description: Hozzáadhat új tanúsítványokat, a helyettesítő tanúsítvány, és távolítsa el a tanúsítványt, illetve a Service Fabric-fürt módját ismerteti.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2018
ms.author: aljo
ms.openlocfilehash: 37140b665f30d8bb99dd49dbdcca265969eaaf2f
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806691"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Adja hozzá, vagy távolítsa el a tanúsítványokat a Service Fabric-fürtön az Azure-ban
Javasoljuk, hogy Ismerkedjen meg hogyan a Service Fabric X.509-tanúsítványokat használ, és ismernie kell a [fürtök – biztonsági helyzetek](service-fabric-cluster-security.md). Ismernie kell a fürt tanúsítványt, és mire használható, mielőtt folytatná.

Az Azure szolgáltatás hálók SDK-k alapértelmezett tanúsítvány betöltési viselkedés az, hogy üzembe helyezése és használata egy meghatározott tanúsítványt amely lejáró dátummal rendelkező a későbbiekben; függetlenül attól, azok elsődleges vagy másodlagos configuration definícióját. Klasszikus viselkedésének való visszaváltás nem javasolt speciális művelet, és beállítása a "UseSecondaryIfNever" beállítás a paraméter értéke hamis belül a Fabric.Code konfiguráció szükséges.

A Service fabric lehetővé teszi két fürttanúsítványok, az elsődleges és a egy másodlagos tanúsítvány biztonsági ügyfél-tanúsítványok mellett a fürt létrehozása során konfigurálásakor adja meg. Tekintse meg [létrehozása egy azure-portálon keresztül fürtön](service-fabric-cluster-creation-via-portal.md) vagy [létrehozása egy azure-fürtön az Azure Resource Manageren keresztül](service-fabric-cluster-creation-via-arm.md) beállításukról a részletekért hozzon létre idő. Ha a fürt csak egy tanúsítványt, létrehozás időpontja, majd használt elsődleges-tanúsítványt. Fürt létrehozása után hozzáadhat egy új tanúsítványt, egy másodlagos.

> [!NOTE]
> Biztonságos fürt esetében mindig szüksége lesz legalább egy érvényes (nem visszavont és nem járt le) fürt telepített tanúsítvány (elsődleges vagy másodlagos) (Ha nem, a fürt leáll a működése). 90 nap elteltével minden érvényes tanúsítvány lejárati, eléri a rendszer létrehoz egy figyelmeztetés a nyomkövetési, és figyelmeztetés állapottal kapcsolatos esemény a csomóponton. Jelenleg nincs e-mail vagy bármely más értesítési, amely ezen a cikken küld a Service Fabric. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>A portál használatával a másodlagos fürttanúsítvány hozzáadása
Az Azure Portalon, az Azure powershell használata a másodlagos fürttanúsítvány nem adható hozzá. A folyamat a rendszer később a jelen dokumentumban vázolt.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>A portál használatával a fürttanúsítvány eltávolítása
Biztonságos fürt esetében mindig szüksége lesz legalább egy érvényes (nem visszavont és nem járt le) tanúsítványra. A tanúsítvány telepítve a jövőbeli lejáró dátumra legtávolabbi lesz használatban, és a fürt működik; eltávolítása teszik Ügyeljen arra, hogy csupán eltávolítja a tanúsítvány lejárt, vagy egy nem használt tanúsítványt, amely a leghamarabb lejár.

Távolítsa el a fel nem használt fürt biztonsági tanúsítványa, nyissa meg a biztonság szakaszában, és válassza a "Törlés" lehetőséget a fel nem használt tanúsítványt a helyi menüből.

Ha a leképezés eltávolítani a tanúsítványt, amely elsődleges van megjelölve, kell helyezheti üzembe, mint az elsődleges tanúsítvány, a jövőben további lejáró dátummal rendelkező másodlagos tanúsítvány automatikus helyettesítő viselkedésének; engedélyezése Törölje az elsődleges tanúsítványt, az automatikus helyettesítő befejeződése után.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Resource Manager Powershell-lel másodlagos tanúsítvány hozzáadása
> [!TIP]
> Most már jobb és egyszerűbb a módon hozzáadni egy másodlagos tanúsítvány használatával a [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) parancsmagot. Nem kell követhesse a jelen szakaszban ismertetett lépéseket.  Emellett nem kell létrehozni és üzembe helyezni a fürt használatakor eredetileg használt sablon a [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) parancsmagot.

A lépések feltételezik, hogy ismeri a Resource Manager működését, és legalább egy Service Fabric-fürtöt egy Resource Manager-sablon használatával telepített, és rendelkezik a sablont, amely hasznos a fürt beállításához használt. Azt is feltételezzük, hogy járatos JSON.

> [!NOTE]
> Ha a minta sablon és paraméterek, amelyek segítségével hajtsa végre a mentén, és kiindulási pontként, majd töltse le a [git-tárház](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>A Resource Manager-sablon szerkesztése

Következő mentén könnyű minta 5 – virtuális gép – 1-NodeType-Secure_Step2.JSON teszi azt minden módosításokat tartalmaz. a minta mindig elérhető legyen [git-tárház](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Ügyeljen arra, hogy a lépéseket**

1. Nyissa meg a Resource Manager-sablon, amellyel-fürt üzembe helyezése meg. (Ha letöltötte a minta az előző adattárból, 5 – virtuális gép – 1-NodeType-Secure_Step1.JSON használatával egy biztonságos fürt üzembe helyezése, és nyissa meg a sablon mentése).

2. Adjon hozzá **két új paraméterrel** "secCertificateThumbprint" és "secCertificateUrlValue", "string" írja a sablon a paraméterek szakaszához. Másolja a következő kódrészletet, és adja hozzá a sablont. A sablon a forrás, attól függően előfordulhat, hogy már ezek definiált, ha úgy helyezze át a következő lépéssel. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Módosítása a **Microsoft.ServiceFabric/clusters** erőforrás - keresse meg az "Microsoft.ServiceFabric/clusters" erőforrás-definíció a sablonban. Annak a definíciónak tulajdonságainál található "Tanúsítványok" JSON-címkével, amely a következő JSON-kódrészletben hasonlóan kell kinéznie:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Adjon hozzá egy új címke "thumbprintSecondary", és adjon meg egy értéket "[parameters('secCertificateThumbprint')]".  

    Szóval most már az erőforrás-definíció a következőhöz hasonlóan kell kinéznie (attól függően, a forrás-sablon, nem lehet az alábbi kódrészlethez hasonlóan). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Ha azt szeretné, hogy **vihetők át a tanúsítványt a**, majd adja meg az új tanúsítvány az elsődleges és áthelyezése az aktuális elsődleges, a másodlagos. Ennek eredményeképpen a Váltás az aktuális elsődleges tanúsítvány az új tanúsítvány egy központi telepítési lépésben.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Változtatások **összes** a **Microsoft.Compute/virtualMachineScaleSets** erőforrás-definíciók – keresse meg a Microsoft.Compute/virtualMachineScaleSets erőforrás-definícióban. Görgessen az "publisher": "Microsoft.Azure.ServiceFabric", under "virtualMachineProfile".

    A Service Fabric közzétevő beállításaiban valami ilyesmit megjelenik.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Az új tanúsítvány-bejegyzés hozzáadása
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    A Tulajdonságok kell kinéznie
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Ha azt szeretné, hogy **vihetők át a tanúsítványt a**, majd adja meg az új tanúsítvány az elsődleges és áthelyezése az aktuális elsődleges, a másodlagos. Ennek eredményeképpen a Váltás az aktuális tanúsítvány az új tanúsítvány egy központi telepítési lépésben.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    A Tulajdonságok kell kinéznie    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Változtatások **összes** a **Microsoft.Compute/virtualMachineScaleSets** erőforrás-definíciók – keresse meg a Microsoft.Compute/virtualMachineScaleSets erőforrás-definícióban. Görgessen az "vaultCertificates":, "OSProfile" alatt. valahogy így kell kinéznie.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Adja hozzá a secCertificateUrlValue azt. a következő kódrészletet használja:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Most az eredményül kapott Json következőhöz hasonlóan kell kinéznie.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Győződjön meg arról, hogy Ön a következő ismétlődő 4. és 5 a Nodetypes/Microsoft.Compute/virtualMachineScaleSets erőforrás definíciókat a sablonban. Ha lemaradt az egyiket, a tanúsítvány nem lekérése telepítve az, hogy a virtuálisgép-méretezési, és hogy kiszámíthatatlan következményekkel járhat a fürtben, beleértve a fürt leállítása előtt (Ha Ön a kialakított nem érvényes tanúsítvány, a fürt biztonsági használhat. Ezért ellenőrizze, mielőtt továbblépne.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>A sablonfájl megfelelően a fentiekben hozzáadott új paraméterek szerkesztése
A minta használata a [git-tárház](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) követéséhez megkezdheti a módosításokat a minta 5 – virtuális gép – 1-NodeType-Secure.parameters_Step2.JSON 

A Resource Manager-sablon paraméter fájl szerkesztése, a két új paraméterek secCertificateThumbprint és secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>A sablon üzembe helyezéséhez az Azure-bA

- Most már készen áll a sablon üzembe helyezéséhez az Azure-bA. Nyisson meg egy Azure PS 1 vagy újabb parancssort.
- Jelentkezzen be az Azure-fiókot, és válassza ki az adott azure-előfizetés. Ez a fontos lépés a azok számára, akik hozzáférhetnek a több mint egy azure-előfizetéssel.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subscription ID> 

```

Tesztelje a sablon üzembe helyezése előtt. Használja ugyanazt az erőforráscsoportot, amely a fürt jelenlegi üzembe.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Az erőforráscsoportba helyezheti üzembe a sablont. Használja ugyanazt az erőforráscsoportot, amely a fürt jelenlegi üzembe. A New-AzureRmResourceGroupDeployment parancs futtatásával. Nem kell megadnia a a módot, mivel az alapértelmezett érték **növekményes**.

> [!NOTE]
> Fejezze be a mód állítja be, ha véletlenül törölheti erőforrások, amelyek nem a sablonban. Ebben a forgatókönyvben azt nem használja.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Íme egy kitöltött példa az ugyanazon PowerShell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Az üzembe helyezés befejezése után csatlakozzon a fürthöz, az új tanúsítványt használja, és hajtsa végre az egyes lekérdezések. Ha Ön tudja megtenni. Ezután törölheti a régi tanúsítványt. 

Ha önaláírt tanúsítványt használ, ne felejtse el a megbízható személyek nevű helyi tanúsítványtár importálja azokat.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Gyors referenciaként itt látható a parancsot egy biztonságos fürthöz való csatlakozáshoz 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Gyors referenciaként itt látható a parancsot a fürt állapotának lekérése

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>A fürt alkalmazás tanúsítványok telepítését.

Az előző lépések 5 leírt módon szeretné, hogy a tanúsítványok, telepítjük a keyvault-ből a csomópontok is használhatja ugyanazokat a lépéseket. Ugyanúgy kell meghatározása és különböző paramétereket használja.


## <a name="adding-or-removing-client-certificates"></a>Hozzáadásával vagy eltávolításával ügyféltanúsítványok

A fürt-tanúsítványok mellett a felügyeleti műveletek végrehajtása a Service Fabric-fürt ügyféltanúsítványok is hozzáadhat.

Kétféle ügyféltanúsítványok - rendszergazda adhat hozzá vagy csak olvasható. Ezek akkor használhatók a felügyeleti műveletek és lekérdezési műveleteket a fürtön való hozzáférés szabályozásához. Alapértelmezés szerint a fürt tanúsítványok kerülnek az engedélyezett felügyeleti tanúsítványok listája.

Ügyféltanúsítványok tetszőleges számú is megadhat. Minden egyes hozzáadása vagy törlése a Service Fabric-fürthöz eredményez a konfiguráció frissítése


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Ügyféltanúsítványok - rendszergazda felvétele vagy írásvédett portálon keresztül

1. Keresse meg a biztonsági szakaszban, és válassza a "+ hitelesítés" gombra a biztonsági szakasz fölött.
2. A hitelesítés hozzáadása területen válassza a "hitelesítési típus" - "Csak olvasható ügyfél" vagy "Rendszergazdai ügyfél"
3. Most válassza ki a hitelesítési módszert. Ez azt jelzi, hogy a Service Fabric-e azt keresi ezt a tanúsítványt a tulajdonos neve vagy az ujjlenyomat használatával. Általában nincs tulajdonos neve, a hitelesítési módszer használata ajánlott biztonsági eljárás. 

![Ügyféltanúsítvány hozzáadása][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Az ügyféltanúsítványok - rendszergazda vagy a portál használatával csak olvasható törlését

Másodlagos tanúsítvány eltávolítása a fürt biztonsági, nyissa meg a biztonság szakaszában használja, és válassza a "Törlés" lehetőséget az adott tanúsítvány a helyi menüből.

## <a name="next-steps"></a>További lépések
Olvassa el az alábbi cikkek kezelő további tájékoztatást:

* [Service Fabric-fürt frissítési folyamat és az Ön elvárásainak](service-fabric-cluster-upgrade.md)
* [Az ügyfelek szerepköralapú hozzáférés beállítása](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


