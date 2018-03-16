---
title: "Azure Service Fabric-fürtben lévő tanúsítványok kezelése |} Microsoft Docs"
description: "Hozzáadhat új tanúsítványokat, a helyettesítő tanúsítvány, és távolítsa el a tanúsítványt, vagy a Service Fabric-fürt ismerteti."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 91adc3d3-a4ca-46cf-ac5f-368fb6458d74
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: chackdan
ms.openlocfilehash: 65ade0f2526bf444c2205c74cce0e20be540998d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Hozzáadhat és eltávolíthat tanúsítványokat a Service Fabric-fürtök az Azure-ban
Javasoljuk, hogy megismerje a módját a Service Fabric X.509-tanúsítványokat használ, és ismernie kell a [fürtök biztonsági forgatókönyveinek](service-fabric-cluster-security.md). Ismernie kell fürt tanúsítvány és felhasználási területének, mielőtt végrehajtásának folytatásához.

A Service fabric lehetővé teszi az ügyfél-tanúsítványok mellett a fürt létrehozása során tanúsítvány biztonsági konfigurálásakor két fürt tanúsítvány, egy elsődleges és másodlagos, adja meg. Tekintse meg [létrehozása egy azure-portálon fürttel](service-fabric-cluster-creation-via-portal.md) vagy [létrehozása az azure-fürttel Azure Resource Manageren keresztül](service-fabric-cluster-creation-via-arm.md) azok beállításával kapcsolatos részleteket létre idő. Ha csak egy fürt tanúsítványt ad meg, létrehozás ideje, majd használt elsődleges tanúsítványként. Fürt létrehozása után hozzáadhat egy új tanúsítványt, a egy másodlagos.

> [!NOTE]
> Biztonságos fürt esetén mindig szüksége lesz legalább egy érvényes (a nem visszavont és a nem lejárt) fürt telepített tanúsítvány (elsődleges vagy másodlagos) (Ha nem, a fürt leáll a működése). 90 napig minden érvényes tanúsítvány elérni lejárati, a rendszer létrehoz egy figyelmeztetés nyomkövetést, és egy figyelmeztetés állapotesemény a csomóponton. Jelenleg nincs e-mail vagy más, ezen a cikken küld a Service Fabric értesítést. 
> 
> 

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>A portál használatával a fürt másodlagos tanúsítvány hozzáadásához
Másodlagos fürt tanúsítvány nem adható hozzá, az Azure-portálon, az Azure powershell használata. A folyamat a dokumentum későbbi szakaszában meghatározott.

## <a name="swap-the-cluster-certificates-using-the-portal"></a>A felcserélendő a fürt tanúsítványokat, a portál használatával
Miután egy másodlagos fürt tanúsítványt, ha szeretne váltani, az elsődleges és másodlagos sikeresen telepített, majd lépjen a biztonsági szakaszra, és felcserélni az elsődleges és másodlagos tanúsítvány a helyi menüben válassza a "Elsődleges felcserélés" lehetőséget tanúsítvány.

![Lapozófájl-kapacitás tanúsítvány][Delete_Swap_Cert]

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Távolítsa el a fürt tanúsítványt, a portál használatával
Biztonságos fürt esetén mindig szüksége lesz legalább egy érvényes (a nem visszavont és a nem lejárt) (elsődleges vagy másodlagos) telepített tanúsítvány ellenkező esetben a fürt megszűnik működni.

Távolítsa el a másodlagos tanúsítvány használatát a fürt biztonsági okokból és a biztonsági szakaszban lép, és a másodlagos tanúsítvány a helyi menüből válassza a "Törlés" lehetőséget.

Ha a szándéka az, eltávolítani a tanúsítványt, amely elsődleges van megjelölve, majd szüksége lesz a másodlagos felcserélése, és törölje a másodlagos, a frissítés befejeződése után.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Erőforrás-kezelő Powershell-lel másodlagos tanúsítvány hozzáadásához
> [!TIP]
> Most már jobb és egyszerűbb módon hozzáadni egy másodlagos tanúsítvány használata a [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) parancsmag. Nem kell kövesse a jelen szakaszban szereplő lépéseket.  Is, nem szükséges a telepítés a fürt létrehozásához használatakor eredetileg használt sablon a [Add-AzureRmServiceFabricClusterCertificate](/powershell/module/azurerm.servicefabric/add-azurermservicefabricclustercertificate) parancsmag.

Ezek a lépések feltételezik, hogy Ön ismeri a Resource Manager működésével és legalább egy Service Fabric-fürt Resource Manager-sablon használatával telepített, és a sablont, amely akkor lesz szüksége a fürt létrehozásakor használt. Az Ön számára elfogadható JSON használatával is feltételezzük.

> [!NOTE]
> Ha a keresett mintasablon és mentén vagy kiindulási pontként használható paramétereket, majd töltse le azt a ez [git-tárház](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>A Resource Manager sablon szerkesztése

A következő mentén megkönnyítése érdekében a minta 5-VM-1-NodeType tulajdonságok értéke-Secure_Step2.JSON frissítjük összes szerkesztést tartalmazza. a minta érhető el: [git-tárház](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample).

**Ügyeljen arra, hogy kövesse a lépéseket**

1. Nyissa meg a Resource Manager-sablon használt fürtök telepítése. (Ha letöltötte a minta az előző tárházból, 5-VM-1-NodeType tulajdonságok értéke-Secure_Step1.JSON segítségével biztonságos fürt központi telepítése, és nyissa meg, hogy a sablon mentése).

2. Adja hozzá **két új paraméterrel** "secCertificateThumbprint" és "secCertificateUrlValue", írja be a "karakterlánc" a sablon paraméter szakaszába. Másolja a következő kódrészletet, és adja hozzá a sablont. Attól függően, hogy a sablon a forráskiszolgálón előfordulhat, hogy már ezek definiált, ha úgy helyezze át a következő lépéssel. 
 
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

3. Módosítja a **Microsoft.ServiceFabric/clusters** erőforrás - keresse meg a "Microsoft.ServiceFabric/clusters" erőforrás-definíció a sablonban. Az adott definíció tulajdonságai "Tanúsítvány" JSON található címke, amely a következő JSON-részlet hasonlóan kell kinéznie:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Új címke "thumbprintSecondary" hozzáadása, és adjon neki egy "[parameters('secCertificateThumbprint')]" értéket.  

    Igen, most az erőforrás-definíció a következőképpen kell kinéznie (attól függően, hogy a forrás-sablon, nem lehet pontosan például az alábbi részlet). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Ha azt szeretné, hogy **át a cert**, majd adja meg az új tanúsítvány az elsődleges és áthelyezése az aktuális elsődleges, a másodlagos. Ennek eredményeképp az aktuális elsődleges tanúsítvány helyettesítő egy központi telepítési lépésben új tanúsítványt.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Módosítja **összes** a **Microsoft.Compute/virtualMachineScaleSets** erőforrás-definíciókban - keresse meg a Microsoft.Compute/virtualMachineScaleSets erőforrás-definícióban. Görgessen az "publisher": "Microsoft.Azure.ServiceFabric", "virtualMachineProfile" alatt.

    A Service Fabric publisher beállításaiban megtekintheti az alábbihoz hasonló.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Az új tanúsítvány-bejegyzések felvétele
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    A Tulajdonságok most példához hasonló
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Ha azt szeretné, hogy **át a cert**, majd adja meg az új tanúsítvány az elsődleges és áthelyezése az aktuális elsődleges, a másodlagos. Ennek eredményeképp az aktuális tanúsítvány helyettesítő egy központi telepítési lépésben új tanúsítványt.     

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

    A Tulajdonságok most példához hasonló    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Módosítja **összes** a **Microsoft.Compute/virtualMachineScaleSets** erőforrás-definíciókban - keresse meg a Microsoft.Compute/virtualMachineScaleSets erőforrás-definícióban. A "vaultCertificates" görgessen:, a "OSProfile". akkor kell kinéznie.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Adja hozzá a secCertificateUrlValue azt. a következő kódrészletet használja:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Most már az eredményül kapott Json kell kinéznie.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Győződjön meg arról, hogy Ön a következő ismétlődő 4. és 5 a Nodetypes/Microsoft.Compute/virtualMachineScaleSets erőforrás definíciókat a sablonban. Ha kihagyná valamelyik őket, a tanúsítvány nem beolvasása telepítve az, hogy a virtuálisgép-méretezési csoport, és hogy előre az eredmény a fürtben, beleértve a fürt is (Ha Ön végül nem érvényes tanúsítványokat, a fürt biztonsági is használhat. Igen ellenőrizze, a folytatás előtt.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>A sablonfájl megfelelően a korábban ismertetett módon hozzáadott új paraméterek szerkesztése
Ha a mintát használ a [git-tárház](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) követéséhez, megkezdheti az módosítsa a minta 5-VM-1-NodeType tulajdonságok értéke-Secure.paramters_Step2.JSON 

A Resource Manager-sablon paraméter fájl szerkesztése, vegye fel a két új secCertificateThumbprint és paramétereinek secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>A sablon telepítéséhez az Azure-bA

- Most már készen áll a sablon telepítéséhez az Azure-bA. Nyisson meg egy Azure PS 1 vagy újabb parancssort.
- Jelentkezzen be az Azure-fiókjába, és válassza ki az adott azure-előfizetés. Ez egy fontos lépés a segítsen, akik hozzáférhetnek a több azure-előfizetés.

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

Tesztelje a sablon üzembe helyezése előtt. Ugyanabban az erőforráscsoportban, amely a fürt jelenleg a rendszer használja.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Az erőforráscsoport a sablon telepítése Ugyanabban az erőforráscsoportban, amely a fürt jelenleg a rendszer használja. Futtassa a New-AzureRmResourceGroupDeployment parancsot. Nem kell megadnia a a módot, mivel az alapértelmezett érték **növekményes**.

> [!NOTE]
> Mód Complete állítja be, ha véletlenül erőforrásokat, amelyek nem szerepelnek a sablon törölheti. Ebben a forgatókönyvben azt nem használja.
> 
> 

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Ez az azonos powershell kitöltött példát.

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

Ha a telepítés befejeződött, csatlakozzon a fürthöz, az új tanúsítványt használja, és hajtsa végre az egyes lekérdezések. Ha tudja megtenni. Törölheti a régi tanúsítvány. 

Ha önaláírt tanúsítványt használ, ne felejtse el a megbízható személyek nevű helyi tanúsítványtároló importálja azokat.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Gyors referenciaként Ez a parancs egy biztonságos fürthöz való kapcsolódás 

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
Gyors referenciaként Ez a parancs használatával beszerezheti a fürt állapota

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-application-certificates-to-the-cluster"></a>A fürt alkalmazás tanúsítványok telepítését.

A portáladatbázis előző 5. lépéseket a csomópontra a keyvault alapján telepített tanúsítványok is használhatja ugyanazokat a lépéseket. ugyanúgy kell definiálására és használjon más paramétereket.


## <a name="adding-or-removing-client-certificates"></a>Hozzáadása vagy eltávolítása az ügyfél-tanúsítványok

A fürt tanúsítvány mellett a Service Fabric-fürt felügyeleti műveletek elvégzéséhez ügyféltanúsítványok is hozzáadhat.

Kétféle ügyféltanúsítványok - rendszergazda adhat hozzá vagy csak olvasható. Ezek ezután használhatók a felügyeleti műveletek és a lekérdezési műveletek a fürthöz való hozzáférés vezérlése érdekében. Alapértelmezés szerint a fürt tanúsítványok hozzáadódnak a megengedett felügyeleti tanúsítványok listája.

megadhatja, hogy minden ügyfél-tanúsítványok száma. A Service Fabric-fürt az egy konfigurációs eredmények minden és törléséről


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Ügyféltanúsítványok - rendszergazda felvétele vagy csak olvasható-portálon

1. Lépjen a biztonsági szakaszra, és válassza a "+ hitelesítés" gombra a biztonsági szakasz fölött.
2. A "Hitelesítés hozzáadása" szakasz válassza ki a "hitelesítés" - "Csak olvasható" vagy "Admin ügyfélszámítógépekhez"
3. Most adja meg a hitelesítési módját. Ez azt jelzi, hogy a Service Fabric e keresi ezt a tanúsítványt a tulajdonos neve vagy az ujjlenyomat használatával. Ez általában nem egy jó biztonsági gyakorlat a tulajdonos neve a hitelesítési módszer használatát. 

![Ügyfél-tanúsítvány hozzáadása][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Ügyféltanúsítványok - rendszergazdai vagy csak olvasható a portál használatával törlése

Távolítsa el a másodlagos tanúsítvány használatát a fürt biztonsági okokból és a biztonsági szakaszban lép, és válassza a "Törlés" lehetőséget az adott tanúsítvány a helyi menüből.

## <a name="next-steps"></a>További lépések
Ezek a cikkek további információt a kiszolgálófürt-felügyelet olvasható:

* [Service Fabric-fürt verziófrissítés folyamatáról és az Ön elvárásainak](service-fabric-cluster-upgrade.md)
* [Az ügyfelek a szerepköralapú hozzáférés-telepítő](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Delete_Swap_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_09.PNG
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


