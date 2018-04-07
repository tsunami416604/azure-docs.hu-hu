---
title: Létrehozása és kezelése az Azure virtuális gép Java használatával |} Microsoft Docs
description: Java és az Azure Resource Manager segítségével telepítheti a virtuális gép és annak támogató erőforrásokat.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
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
ms.author: davidmu
ms.openlocfilehash: da8f8b0e84a51683bd4f631832e1f319f3a9bbb0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Létrehozása és kezelése Windows-alapú virtuális gépek az Azure-ban Java

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) több támogató Azure-erőforrások kell. Ez a cikk ismerteti a létrehozása, kezelése és a Java Virtuálisgép-erőforrások törlése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Maven-projekt létrehozása
> * Adja hozzá a függőségek
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

A lépések elvégzéséhez körülbelül 20 percet vesz igénybe.

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

1. Ha még nem tette meg, telepítse [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
2. Telepítés [Maven](http://maven.apache.org/download.cgi).
3. Hozzon létre egy új mappát és a projekthez:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Adja hozzá a függőségek

1. Az a `testAzureApp` mappa, nyissa meg a `pom.xml` fájlt, és a build konfigurációját, és &lt;projekt&gt; ahhoz, hogy az alkalmazás összeállítása:

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

Ez a lépés megkezdése előtt győződjön meg arról, hogy rendelkezik-e a hozzáférést egy [Active Directory szolgáltatás egyszerű](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Is rögzíteni kell az alkalmazás Azonosítóját, a hitelesítési kulcs és a bérlő azonosítója, amelyekre szüksége van egy későbbi lépésben.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

1. Hozzon létre egy fájlt `azureauth.properties` , és ezek a tulajdonságok felvétele:

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

    Cserélje le **&lt;előfizetés-azonosító&gt;** az előfizetés-azonosítóval rendelkező **&lt;alkalmazásazonosító&gt;** való a Active Directory-azonosítót, **&lt;hitelesítési kulcs&gt;** az alkalmazás kulccsal és **&lt;bérlőazonosító&gt;** a bérlő azonosítója.

2. Mentse a fájlt.
3. A fájl teljes elérési útját a hitelesítés a rendszerhéj AZURE_AUTH_LOCATION nevű környezeti változó értéke.

### <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa meg a `App.java` a fájl `src\main\java\com\fabrikam` , és győződjön meg arról, hogy a csomag utasítás szerepel a szabálylista:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. A csomag utasítás alatt adja hozzá ezek kimutatások importálása:
   
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

2. Az Active Directory hitelesítő adatait hozhatják létre, meg kell győződnie kérelmeket, adja hozzá ezt a kódot a fő metódus App osztály:
   
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

Adja meg az alkalmazás az értékét, és az erőforráscsoport létrehozása, vegye fel ezt a kódot a try blokk fő metódus:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>A rendelkezésre állási csoport létrehozása

[Rendelkezésre állási készletek](tutorial-availability-sets.md) megkönnyíti, hogy a virtuális gépeket, amelyet az alkalmazás karbantartása.

A rendelkezésre állási csoport létrehozása, vegye fel ezt a kódot a try blokk a fő metódusban:

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

A nyilvános IP-cím a virtuális gép létrehozása, vegye fel ezt a kódot a try blokk a fő metódusban:

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

Az alhálózat szerepelnie kell egy virtuális gépet egy [virtuális hálózati](../../virtual-network/virtual-networks-overview.md).

Hozzon létre egy alhálózatot és egy virtuális hálózatot, vegye fel ezt a kódot a try blokk a fő metódusban:

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

### <a name="create-the-network-interface"></a>A hálózati illesztő létrehozása

A virtuális gépek kell a hálózati adaptert a virtuális hálózaton való kommunikációhoz.

Hozzon létre egy hálózati adapter, vegye fel ezt a kódot a try blokk a fő metódusban:

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

Most, hogy létrehozta a támogató erőforrásokat, létrehozhat egy virtuális gépet.

A virtuális gép létrehozásához, vegye fel ezt a kódot a try blokk a fő metódusban:

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
> Ebben az oktatóanyagban létrehoz egy virtuális gépet, a Windows Server operációs rendszer verziója. Más rendszerképek kiválasztásáról kapcsolatos további információkért lásd: [keresse meg és válassza ki azokat a Windows PowerShell és az Azure CLI Azure virtuális gép lemezképeket](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
>

Ha szeretné használni a meglévő lemez Piactéri rendszerkép helyett, használja ezt a kódot: 

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

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Emellett érdemes bonyolult vagy ismétlődő feladatok automatizálásához kódot létrehozni.

Ha semmit a a virtuális Géphez van szüksége, kell egy példányát. Ez a kód hozzáadása a try blokk a fő metódus:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>A virtuális gép adatainak beolvasása

A virtuális géppel kapcsolatos információk beszerzéséhez vegye fel ezt a kódot a try blokk fő metódus:

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

Állítsa le a virtuális gépet és a beállítások megőrzése, de továbbra is azt számlázni, vagy állítsa le a virtuális gépet, és azt felszabadítani. Ha egy virtuális gép fel van szabadítva, vele társított összes erőforrás is felszabadított és számlázási végpontjainak.

A virtuális gép leállítása nélkül felszabadítása azt, vegye fel ezt a kódot a try blokk a fő metódusban:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Ha a virtuális gép felszabadítása, módosítsa ezt a kódot kikapcsolt hívása:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>Indítsa el a virtuális Gépet

Indítsa el a virtuális gépet, vegye fel ezt a kódot a try blokk a fő metódusban:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>A virtuális gép átméretezésével

Telepítési sok szempontját figyelembe kell venni, amikor eldönti, a virtuális gép méretét. További információkért lásd: [Virtuálisgép-méretek](sizes.md).  

A virtuális gép Oldalméret módosítása oly módon, vegye fel ezt a kódot a try blokk a fő metódusban:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális gép

Adatlemez hozzáadása a virtuális gépet, amely pedig 2 GB-nál, 0 és ReadWrite gyorsítótárazási típusú logikai egység tartozik, vegye fel ezt a kódot a try blokk a fő metódusban:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokhoz van szó, ajánlott mindig törli az erőforrást, amely már nem szükséges. Ha törölni szeretné a virtuális gépek és a támogató erőforrásokat, meg kell nyitnia csak törölje a csoportot.

1. Törölje a csoportot, vegye fel ezt a kódot a try blokk a fő metódusban:
   
```java
System.out.println("Deleting resources...");
azure.resourceGroups().deleteByName("myResourceGroup");
```

2. Mentse a App.java fájlt.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Öt perc a konzol alkalmazás teljesen futtatásához indítás kell vennie a befejezéshez.

1. Az alkalmazás futtatásához használja a Maven-parancsot:

    ```
    mvn compile exec:java
    ```

2. Ahhoz, hogy nyomja le az ENTER **Enter** erőforrások törlése elindításához eltarthat néhány percig az Azure-portálon az erőforrások létrehozásának ellenőrzése. Kattintson a telepítés állapota a telepítéssel kapcsolatos információk megjelenítéséhez.


## <a name="next-steps"></a>További lépések
* További információ a [Azure Java-könyvtárakban](https://docs.microsoft.com/java/azure/java-sdk-azure-overview).

