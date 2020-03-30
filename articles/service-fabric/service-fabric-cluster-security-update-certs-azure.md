---
title: Tanúsítványok kezelése Azure Service Fabric-fürtben
description: Ez a témakör azt ismerteti, hogy miként adhat hozzá új tanúsítványokat, görgetőtanúsítványokat, és hogyan távolíthatel el tanúsítványt egy Service Fabric-fürtbe vagy onnan.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: a3c92e1b39261af32085e4d9b6cb2462d5c0eb64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458347"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Tanúsítványok hozzáadása és törlése Service Fabric-fürtön az Azure-ban
Javasoljuk, hogy ismerkedjen meg azzal, hogy a Service Fabric hogyan használja az X.509-es tanúsítványokat, és ismerje meg a [fürt biztonsági forgatókönyveit.](service-fabric-cluster-security.md) A folytatás előtt ismernie kell, hogy mi az a fürttanúsítvány, és mire használják.

Az Azure Service Fabrics SDK alapértelmezett tanúsítványbetöltési viselkedése a megadott tanúsítvány üzembe helyezése és használata a jövőben legtávolabbi lejárati dátummal; az elsődleges vagy másodlagos konfigurációdefiníciótól függetlenül. A klasszikus viselkedésre való visszalépés nem ajánlott speciális művelet, és a konfiguráción belül a `Fabric.Code` "UseSecondaryIfNewer" paraméterérték hamis beállításának beállítását igényli.

A Szolgáltatásháló lehetővé teszi két fürttanúsítvány, egy elsődleges és egy másodlagos megadását, amikor a fürt létrehozása során konfigurálja a tanúsítványbiztonságot, az ügyféltanúsítványok mellett. Tekintse meg [egy azure-fürt létrehozása portálon keresztül,](service-fabric-cluster-creation-via-portal.md) vagy [hozzon létre egy azure-fürt az Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) használatával a beállításukról a létrehozási időben. Ha létrehozáskor csak egy fürttanúsítványt ad meg, akkor az lesz az elsődleges tanúsítvány. A fürt létrehozása után másodlagosan új tanúsítványt adhat hozzá.

> [!NOTE]
> Biztonságos fürt esetén mindig szüksége lesz legalább egy érvényes (nem visszavont és nem lejárt) fürttanúsítványra (elsődleges vagy másodlagos) (ha nem, a fürt működése leáll). 90 nappal azelőtt, hogy az összes érvényes tanúsítvány eléri a lejárati idő, a rendszer létrehoz egy figyelmeztető nyomkövetést és egy figyelmeztető állapoteseményt a csomóponton. Jelenleg nincs e-mail, vagy bármely más értesítést, amely a Service Fabric küld ki ezt a cikket. 
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Másodlagos fürttanúsítvány hozzáadása a portál használatával
Másodlagos fürttanúsítvány nem adható hozzá az Azure Portalon keresztül, használja az Azure powershell. A folyamat későbbi részében ismertetésre kerül.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Fürttanúsítvány eltávolítása a portál használatával
Biztonságos fürt esetén mindig szüksége lesz legalább egy érvényes (nem visszavont és nem lejárt) tanúsítványra. A tanúsítvány telepítve a legtávolabbi a jövőben lejárati dátum lesz használatban, és eltávolítja azt teszi a fürt működését; győződjön meg arról, hogy csak a lejárt tanúsítványt vagy a hamarosan lejáró, nem használt tanúsítványt távolítja el.

A nem használt fürtbiztonsági tanúsítvány eltávolításához keresse meg a Biztonság szakaszt, és válassza a "Törlés" lehetőséget a nem használt tanúsítvány helyi menüjében.

Ha a szándék az elsődlegesként megjelölt tanúsítvány eltávolítása, akkor egy másodlagos tanúsítványt kell telepítenie egy lejárati dátummal a jövőben, mint az elsődleges tanúsítvány, amely lehetővé teszi az automatikus váltási viselkedést; törölje az elsődleges tanúsítványt az automatikus átgörgetés befejezése után.

## <a name="add-a-secondary-certificate-using-resource-manager-powershell"></a>Másodlagos tanúsítvány hozzáadása az Erőforrás-kezelő Powershell használatával
> [!TIP]
> Most már van egy jobb és egyszerűbb módja annak, hogy az [Add-AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) parancsmag használatával másodlagos tanúsítványt adjon hozzá. Nem kell végrehajtania az ebben a szakaszban ismertetett további lépéseket.  Emellett az [Add-AzServiceFabricClusterCertificate](/powershell/module/az.servicefabric/add-azservicefabricclustercertificate) parancsmag használatakor nincs szükség a fürt létrehozásához és üzembe helyezéséhez eredetileg használt sablonra.

Ezek a lépések feltételezik, hogy ismeri az Erőforrás-kezelő működését, és legalább egy Service Fabric-fürtöt telepített egy Erőforrás-kezelő sablon használatával, és a fürt beállításához használt sablonnal rendelkezik. Azt is feltételezzük, hogy ön kényelmes használata JSON.

> [!NOTE]
> Ha keres egy minta sablont és paramétereket, amelyek segítségével követni mentén, vagy kiindulási pontként, majd töltse le ezt a [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Az Erőforrás-kezelő sablon szerkesztése

A további műveletek megkönnyítése érdekében az 5-VM-1-NodeTypes-Secure_Step2.JSON mintát tartalmazza az összes általunk végzett szerkesztést. a minta [elérhető git-repo.](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample)

**Ügyeljen arra, hogy kövesse az összes lépést**

1. Nyissa meg a fürt telepítéséhez használt Erőforrás-kezelő sablont. (Ha letöltötte a mintát az előző tárból, majd használja az 5-VM-1-NodeTypes-Secure_Step1.JSON-t egy biztonságos fürt központi telepítéséhez, majd nyissa meg a sablont).

2. Adjon hozzá két új "secCertificateThumbprint" és "secCertificateUrlValue" típusú "string" **paramétert** a sablon paraméterszakaszához. A következő kódrészletet másolhatja, és hozzáadhatja a sablonhoz. A sablon forrásától függően előfordulhat, hogy ezeket már definiálta, ha igen, lépjen a következő lépésre. 
 
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

3. Módosítsa a **Microsoft.ServiceFabric/clusters** erőforrást – Keresse meg a "Microsoft.ServiceFabric/clusters" erőforrás-definíciót a sablonban. A definíció tulajdonságai alatt megtalálja a "Tanúsítvány" JSON címkét, amely a következő JSON-kódrészlethez hasonlóan fog kinézni:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Adjon hozzá egy új "thumbprintSecondary" címkét, és adjon neki egy "[parameters('secCertificateThumbprint')]"értéket.  

    Így most az erőforrás-definíciónak a következőképpen kell kinéznie (a sablon forrásától függően előfordulhat, hogy nem pontosan olyan, mint az alábbi kódrészlet). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Ha át szeretné **görgetni a tanúsítványt,** majd adja meg az új tanúsítványt elsődlegesként, és az aktuális elsődleges másodlagosként való áthelyezését. Ez azt eredményezi, hogy az aktuális elsődleges tanúsítvány egy központi telepítési lépésben átkerül az új tanúsítványba.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Módosítsa az **összes** **Microsoft.Compute/virtualMachineScaleSets** erőforrás-definíciót – Keresse meg a Microsoft.Compute/virtualMachineScaleSets erőforrás-definíciót. Görgessen a "kiadó": "Microsoft.Azure.ServiceFabric", a "virtualMachineProfile".

    A Service Fabric közzétevői beállításaiban valami ilyesmit kell látnia.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    Az új tanúsítványbejegyzések hozzáadása
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    A tulajdonságoknak így kell kinézniük:
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Ha át szeretné **görgetni a tanúsítványt,** majd adja meg az új tanúsítványt elsődlegesként, és az aktuális elsődleges másodlagosként való áthelyezését. Ez azt eredményezi, hogy az aktuális tanúsítvány egy központi telepítési lépésben átkerül az új tanúsítványba.     

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

    A tulajdonságoknak így kell kinézniük:    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Módosítsa az **összes** **Microsoft.Compute/virtualMachineScaleSets** erőforrás-definíciót – Keresse meg a Microsoft.Compute/virtualMachineScaleSets erőforrás-definíciót. Görgessen a "vaultCertificates": , az "OSProfile" területen. Valahogy így kell kinéznie.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Adja hozzá a secCertificateUrlValue értéket. használja a következő kódrészletet:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    Most az így létrejövő Jsonnak valahogy így kell kinéznie.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Győződjön meg arról, hogy a 4. Ha egyiket kihagyja, a tanúsítvány nem lesz telepítve a virtuálisgép-méretezési csoportra, és előre nem látható eredményeket fog kapni a fürtben, beleértve a fürt leépülése (ha a végén nincs érvényes tanúsítvány, amelyet a fürt biztonsági célokra használhat. Tehát ellenőrizze, mielőtt folytatná.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>A sablonfájl szerkesztése a fent hozzáadott új paramétereknek megfelelően
Ha a [git-repo](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) mintáját használja a folytatáshoz, elkezdheti a módosításokat a Minta 5-VM-1-NodeTypes-Secure.parameters_Step2.JSON 

Edit your Resource Manager Template parameter File, add hozzá a két új paramétersecCertificateThumbprint és secCertificateUrlValue. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>A sablon üzembe helyezése az Azure-ban

- Most már készen áll a sablon azure-ba való üzembe helyezésére. Nyisson meg egy Azure PS 1+ verziójú parancssort.
- Jelentkezzen be az Azure-fiókjába, és válassza ki az adott azure-előfizetést. Ez egy fontos lépés azok számára, akik egynél több azure-előfizetéshez férnek hozzá.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Tesztelje a sablont az üzembe helyezés előtt. Használja ugyanazt az erőforráscsoportot, amelybe a fürt jelenleg telepítve van.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Telepítse a sablont az erőforráscsoportba. Használja ugyanazt az erőforráscsoportot, amelybe a fürt jelenleg telepítve van. Futtassa a New-AzResourceGroupDeployment parancsot. Nem kell megadnia a módot, mivel az alapértelmezett érték **növekményes**.

> [!NOTE]
> Ha a Mód beállítást Teljes beállításra állítja, véletlenül törölheti azokat az erőforrásokat, amelyek nem szerepelnek a sablonban. Tehát ne használja ebben a forgatókönyvben.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Itt van egy kitöltött példa az azonos powershell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

A központi telepítés befejezése után csatlakozzon a fürthöz az új tanúsítvánnyal, és hajtson végre néhány lekérdezést. Ha képes vagy rá. Ezután törölheti a régi tanúsítványt. 

Ha önaláírt tanúsítványt használ, ne felejtse el importálni őket a helyi TrustedPeople tanúsítványtárolóba.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
A gyors hivatkozás itt a parancs, hogy csatlakozzon egy biztonságos fürt 

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
A gyors hivatkozás itt a parancs, hogy a fürt állapota

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Ügyféltanúsítványok telepítése a fürtre.

Az előző 5. Csak meg kell határoznia és használnia kell a különböző paramétereket.


## <a name="adding-or-removing-client-certificates"></a>Ügyféltanúsítványok hozzáadása és eltávolítása

A fürttanúsítványokon kívül ügyféltanúsítványokat is hozzáadhat a Service Fabric-fürt felügyeleti műveleteinek végrehajtásához.

Kétféle ügyféltanúsítványt adhat hozzá – rendszergazda vagy írásvédett. Ezek segítségével szabályozhatja a fürt felügyeleti műveleteihez és lekérdezési műveleteihez való hozzáférést. Alapértelmezés szerint a fürttanúsítványok hozzáadódnak az engedélyezett rendszergazdai tanúsítványok listájához.

tetszőleges számú ügyféltanúsítványt adhat meg. Minden egyes hozzáadás/törlés a Service Fabric-fürt konfigurációs frissítését eredményezi


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Ügyféltanúsítványok hozzáadása – Rendszergazda vagy írásvédett portálon keresztül

1. Nyissa meg a Biztonsági szakaszt, és kattintson a "+ hitelesítés" gombra a biztonsági szakasz tetején.
2. A "Hitelesítés hozzáadása" szakaszban válassza a "Hitelesítés típusa" - "Írásvédett ügyfél" vagy "Admin client" lehetőséget.
3. Most válassza ki az engedélyezési módszert. Ez azt jelzi, hogy a Service Fabric, hogy meg kell-e keresni ezt a tanúsítványt a tulajdonos nevét vagy az ujjlenyomatot. Általában nem jó biztonsági gyakorlat a tulajdonos nevének engedélyezési módszerét használni. 

![Ügyféltanúsítvány hozzáadása][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Ügyféltanúsítványok törlése – rendszergazda vagy írásvédett a portál használatával

Ha el szeretne távolítani egy másodlagos tanúsítványt a fürtbiztonsághoz való használatból, keresse meg a Biztonság szakaszt, és válassza a "Törlés" lehetőséget az adott tanúsítvány helyi menüjében.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Alkalmazástanúsítványok hozzáadása virtuálisgép-méretezési csoporthoz

Az alkalmazásokhoz használt tanúsítvány fürtre való üzembe helyezéséhez tekintse meg [ezt a Powershell-parancsfájlmintát.](scripts/service-fabric-powershell-add-application-certificate.md)

## <a name="next-steps"></a>További lépések
A fürtkezelésről az alábbi cikkekben olvashat bővebben:

* [A Service Fabric fürtfrissítési folyamata és az Ön tőlünk kapott elvárások](service-fabric-cluster-upgrade.md)
* [Szerepköralapú hozzáférés beállítása az ügyfelek számára](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


