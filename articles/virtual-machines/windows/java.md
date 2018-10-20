---
title: Létrehozása és kezelése az Azure virtuális gép Javás környezetekben |} A Microsoft Docs
description: A Java és a egy virtuális gép és a támogató erőforrások üzembe helyezése Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: zarhoads
ms.openlocfilehash: 4495b6813a3936967ceb81beb216850538b04af5
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465593"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Létrehozása és kezelése Windows virtuális gépek az Azure-ban a Java használatával

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) több támogató Azure-erőforrásokat kell. Ez a cikk ismerteti a létrehozását, kezelését és a Java Virtuálisgép-erőforrások törlése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzon létre egy Maven-projektet
> * Függőségek hozzáadása
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Körülbelül 20 perc alatt elvégezheti ezeket a lépéseket vesz igénybe.

## <a name="create-a-maven-project"></a>Hozzon létre egy Maven-projektet

1. Ha ezt még nem tette meg, telepítse a [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
2. Telepítés [Maven](http://maven.apache.org/download.cgi).
3. Hozzon létre egy új mappát és a projekthez:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. Alatt a `testAzureApp` mappában nyissa meg a `pom.xml` fájlt, és a build konfigurációját úgy &lt;projekt&gt; ahhoz, hogy az alkalmazás létrehozását:

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. Adja a függőségeket, az Azure Java SDK eléréséhez szükséges.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-compute</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-resources</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-mgmt-network</artifactId>
      <version>1.1.0</version>
    </dependency>
    <dependency>
      <groupId>com.squareup.okio</groupId>
      <artifactId>okio</artifactId>
      <version>1.13.0</version>
    </dependency>
    <dependency> 
      <groupId>com.nimbusds</groupId>
      <artifactId>nimbus-jose-jwt</artifactId>
      <version>3.6</version>
    </dependency>
    <dependency>
      <groupId>net.minidev</groupId>
      <artifactId>json-smart</artifactId>
      <version>1.0.6.3</version>
    </dependency>
    <dependency>
      <groupId>javax.mail</groupId>
      <artifactId>mail</artifactId>
      <version>1.4.5</version>
    </dependency>
    ```

3. Mentse a fájlt.

## <a name="create-credentials"></a>Hitelesítő adatok létrehozása

Ebben a lépésben a Kezdés előtt győződjön meg arról, hogy rendelkezik-e a hozzáférést egy [Active Directory egyszerű szolgáltatás](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Egy későbbi lépésben is az Alkalmazásazonosítót, a hitelesítési kulcsot és a bérlő Azonosítóját kell kell rögzíteni.

### <a name="create-the-authorization-file"></a>Az engedélyezési-fájl létrehozása

1. Hozzon létre egy fájlt `azureauth.properties` , és ezek a Tulajdonságok hozzáadása:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    Cserélje le **&lt;előfizetés-azonosító&gt;** az előfizetés-azonosítójú **&lt;alkalmazásazonosító&gt;** az Active Directory-alkalmazással azonosító, **&lt;hitelesítési kulcs&gt;** az alkalmazás kulccsal és **&lt;bérlőazonosító&gt;** a bérlői azonosító.

2. Mentse a fájlt.
3. A hitelesítési fájl teljes elérési útja a parancshéjban AZURE_AUTH_LOCATION nevű környezeti változó értéke.

### <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a `App.java` fájlt `src\main\java\com\fabrikam` és ellenőrizze, hogy a csomag nyilatkozat tetején is:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. A csomag utasítás alatt adja hozzá ezeket kimutatások importálása:
   
    ```java
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.compute.AvailabilitySet;
    import com.microsoft.azure.management.compute.AvailabilitySetSkuTypes;
    import com.microsoft.azure.management.compute.CachingTypes;
    import com.microsoft.azure.management.compute.InstanceViewStatus;
    import com.microsoft.azure.management.compute.DiskInstanceView;
    import com.microsoft.azure.management.compute.VirtualMachine;
    import com.microsoft.azure.management.compute.VirtualMachineSizeTypes;
    import com.microsoft.azure.management.network.PublicIPAddress;
    import com.microsoft.azure.management.network.Network;
    import com.microsoft.azure.management.network.NetworkInterface;
    import com.microsoft.azure.management.resources.ResourceGroup;
    import com.microsoft.azure.management.resources.fluentcore.arm.Region;
    import com.microsoft.azure.management.resources.fluentcore.model.Creatable;
    import com.microsoft.rest.LogLevel;
    import java.io.File;
    import java.util.Scanner;
    ```

2. Az Active Directorybeli hitelesítő adatokat igénylő kérelmeket létrehozásához adja hozzá ezt a kódot a fő metódus az alkalmazás osztály:
   
    ```java
    try {    
        final File credFile = new File(System.getenv("AZURE_AUTH_LOCATION"));
        Azure azure = Azure.configure()
            .withLogLevel(LogLevel.BASIC)
            .authenticate(credFile)
            .withDefaultSubscription();
    } catch (Exception e) {
        System.out.println(e.getMessage());
        e.printStackTrace();
    }

    ```

## <a name="create-resources"></a>Erőforrások létrehozása

### <a name="create-the-resource-group"></a>Az erőforráscsoport létrehozása

Minden erőforrás tartalmaznia kell egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md).

Adja meg az értékeket az alkalmazást, és hozza létre az erőforráscsoportot, adja hozzá a kódot a fő módszer bloku:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>A rendelkezésre állási csoport létrehozása

[A rendelkezésre állási csoportok](tutorial-availability-sets.md) megkönnyíti, hogy a virtuális gépek, amelyet az alkalmazás karbantartása.

A rendelkezésre állási készlet létrehozásához, adja hozzá a kódot a fő módszer bloku:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>A nyilvános IP-cím létrehozása

A [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) kommunikálni a virtuális gép van szükség.

Szeretne létrehozni a virtuális gép nyilvános IP-címét, adja hozzá a kódot a fő módszer bloku:

```java
System.out.println("Creating public IP address...");
PublicIPAddress publicIPAddress = azure.publicIPAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Virtuális gép, egy alhálózaton kell lennie egy [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

Hozhat létre egy alhálózatot és egy virtuális hálózatot, adja hozzá a kódot a fő módszer bloku:

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet","10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>A hálózati adapter létrehozása

A hálózati adaptert a virtuális hálózaton kommunikálni valamelyik virtuális gépnél.

Hozzon létre egy hálózati adaptert, adja hozzá ezt a kódot, a fő módszer bloku:

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

A támogató erőforrások létrehozása, létrehozhat egy virtuális gépet.

A virtuális gép létrehozásához adja hozzá ezt a kódot, a fő módszer bloku:

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> Ebben az oktatóanyagban létrehoz egy virtuális gépet, a Windows Server operációs rendszer verzióját futtatja. További rendszerképeket kiválasztásával kapcsolatos további tudnivalókért lásd: [és a Windows PowerShell és az Azure CLI Azure-beli virtuálisgép-rendszerképek kiválasztása](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Ha szeretné használni a meglévő lemez Piactéri lemezképet helyett, akkor használja ezt a kódot: 

```java
ManagedDisk managedDisk = azure.disks.define("myosdisk") 
    .withRegion(Region.US_EAST) 
    .withExistingResourceGroup("myResourceGroup") 
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd") 
    .withSizeInGB(128) 
    .withSku(DiskSkuTypes.PremiumLRS) 
    .create(); 

azure.virtualMachines.define("myVM") 
    .withRegion(Region.US_EAST) 
    .withExistingResourceGroup("myResourceGroup") 
    .withExistingPrimaryNetworkInterface(networkInterface) 
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.Windows) 
    .withExistingAvailabilitySet(availabilitySet) 
    .withSize(VirtualMachineSizeTypes.StandardDS1) 
    .create(); 
``` 

## <a name="perform-management-tasks"></a>Felügyeleti feladatok végrehajtása

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Ezenkívül előfordulhat, hogy szeretne létrehozni a kód ismétlődő vagy bonyolult feladatok automatizálására.

Semmit a virtuális gép van szüksége, ha le szeretné kérni egy példányát. Adja hozzá a kódot a fő módszer bloku:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>A virtuális gép adatainak lekérése

Információt szeretne kapni a virtuális gépet, adja hozzá a kódot a fő módszer bloku:

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println("    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for(InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for(DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for(InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for(InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();   
```

### <a name="stop-the-vm"></a>A virtuális gép leállítása

Virtuális gép leállítása és tartsa az összes beállítást, de továbbra is kell fizetnie, vagy egy virtuális gép leállítása és felszabadítása azt. Ha egy virtuális gép fel van szabadítva, az ahhoz kapcsolódó összes erőforrás is felszabadított és számlázási végpontjainak lesznek.

Le szeretné állítani a virtuális gép felszabadítása, anélkül, adja hozzá a kódot a fő módszer bloku:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Szabadítsa fel a virtuális gépet szeretne, ha módosítsa ezt a kódot kikapcsolt meghívásához:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>A virtuális gép elindítása

Indítsa el a virtuális gépet, adja hozzá ezt a kódot, a fő módszer bloku:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

Bizonyos szempontokból az üzembe helyezés során a megfelelő méret a virtuális gép kell tekinteni. További információkért lásd: [Virtuálisgép-méretek](sizes.md).  

A virtuális gép méretének módosításához adja hozzá a kódot a fő módszer bloku:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális Géphez

A virtuális gép, amely 2 GB méretű, adatlemez hozzáadása egy LUN 0 és a egy olvasási és írási gyorsítótárazás típusú rendelkezik, adja hozzá a kódot a fő módszer bloku:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrások díjkötelesek, mindig érdemes mindig törölheti az erőforrást, amely már nincs rá szükség. Ha törölni szeretné a virtuális gépek és a támogató erőforrások, be kell csak törölje az erőforráscsoportot.

1. Törölje az erőforráscsoportot, adja hozzá ezt a kódot, a fő módszer bloku:
   
```java
System.out.println("Deleting resources...");
azure.resourceGroups().deleteByName("myResourceGroup");
```

2. Mentse az App.java fájlt.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Nagyjából öt perc alatt az a Konzolalkalmazás futtatása teljesen le kell vennie a befejezéshez.

1. Az alkalmazás futtatásához használja a Maven-parancsot:

    ```
    mvn compile exec:java
    ```

2. Mielőtt lenyomja **Enter** indítása erőforrás törlése, eltarthat néhány percig, ellenőrizze az az Azure Portalon az erőforrások létrehozását. Kattintson a telepítés állapota, az üzembe helyezéssel kapcsolatos információk megtekintéséhez.


## <a name="next-steps"></a>További lépések
* További információ a [Javához készült Azure-kódtárak](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

