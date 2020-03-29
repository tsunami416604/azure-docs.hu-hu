---
title: Azure-beli virtuális gép létrehozása és kezelése Java használatával
description: A Java és az Azure Resource Manager segítségével üzembe helyezhet egy virtuális gépet és annak összes támogató erőforrását.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 35d5569cb36cb538585b9d2c85a392b668e9fc34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944485"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>Windows-virtuális gépek létrehozása és kezelése az Azure-ban Java használatával

Egy [Azure virtuális gép](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) szüksége van több támogató Azure-erőforrások. Ez a cikk a virtuális gép erőforrások Java használatával történő létrehozását, kezelését és törlését ismerteti. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Maven-projekt létrehozása
> * Függőségek hozzáadása
> * Hitelesítő adatok létrehozása
> * Erőforrások létrehozása
> * Felügyeleti feladatok végrehajtása
> * Erőforrások törlése
> * Az alkalmazás futtatása

Ezeket a lépéseket körülbelül 20 perc alatt kell megtennie.

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

1. Ha még nem tette meg, telepítse a [Java](https://aka.ms/azure-jdks)alkalmazást.
2. Telepítse [a Maven](https://maven.apache.org/download.cgi).
3. Hozzon létre egy új mappát és a projektet:
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. A `testAzureApp` mappa alatt `pom.xml` nyissa meg a fájlt,&gt; és adja hozzá a buildkonfigurációt a projekthez &lt;az alkalmazás létrehozásához:

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

2. Adja hozzá az Azure Java SDK eléréséhez szükséges függőségeket.

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

A lépés megkezdése előtt győződjön meg arról, hogy rendelkezik-e hozzáféréssel egy [Active Directory egyszerű szolgáltatáshoz.](../../active-directory/develop/howto-create-service-principal-portal.md) Az alkalmazásazonosítót, a hitelesítési kulcsot és a bérlői azonosítót is rögzítenie kell egy későbbi lépésben.

### <a name="create-the-authorization-file"></a>Az engedélyezési fájl létrehozása

1. Hozzon létre `azureauth.properties` egy nevű fájlt, és adja hozzá a következő tulajdonságokat:

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.microsoft.com/
    ```

    Cserélje le ** &lt;az&gt; előfizetés-id-t** az előfizetés-azonosítóra, ** &lt;az alkalmazásazonosítót&gt; ** az Active Directory alkalmazásazonosítóra, ** &lt;&gt; ** a hitelesítési kulcsot az alkalmazáskulcsra és ** &lt;a bérlőazonosítót&gt; ** a bérlői azonosítóra.

2. Mentse a fájlt.
3. Állítson be egy AZURE_AUTH_LOCATION nevű környezeti változót a rendszerhéjban a hitelesítési fájl teljes elérési úttal.

### <a name="create-the-management-client"></a>A felügyeleti ügyfél létrehozása

1. Nyissa `App.java` meg `src\main\java\com\fabrikam` a fájl alatt, és győződjön meg arról, hogy ez a csomag utasítás a tetején:

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. A csomagutasítás alatt adja hozzá a következő importálási nyilatkozatokat:
   
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

2. A kérelmekhez szükséges Active Directory-hitelesítő adatok létrehozásához adja hozzá ezt a kódot az alkalmazásosztály fő metódusához:
   
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

Minden erőforrásnak [erőforráscsoportban](../../azure-resource-manager/management/overview.md)kell lennie.

Az alkalmazás értékeinek megadásához és az erőforráscsoport létrehozásához adja hozzá ezt a kódot a fő metódus kipróbálási blokkjához:

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>Az elérhetőségi készlet létrehozása

[A rendelkezésre állási készletek](tutorial-availability-sets.md) megkönnyítik az alkalmazás által használt virtuális gépek karbantartását.

Az elérhetőségi készlet létrehozásához adja hozzá ezt a kódot a fő metódus kipróbálási blokkjához:

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withSku(AvailabilitySetSkuTypes.MANAGED)
    .create();
```
### <a name="create-the-public-ip-address"></a>Nyilvános IP-cím létrehozása

A virtuális géppel való kommunikációhoz [nyilvános IP-cím](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) szükséges.

A virtuális gép nyilvános IP-címének létrehozásához adja hozzá ezt a kódot a fő metódus próbablokkjához:

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

A virtuális gépnek a virtuális [hálózat](../../virtual-network/virtual-networks-overview.md)alhálózatában kell lennie.

Alhálózat és virtuális hálózat létrehozásához adja hozzá ezt a kódot a fő metódus próbablokkjához:

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

A virtuális gépnek hálózati adapterre van szüksége a virtuális hálózaton való kommunikációhoz.

Hálózati adapter létrehozásához adja hozzá ezt a kódot a fő metódus próbablokkjához:

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

Most, hogy létrehozta az összes támogató erőforrást, létrehozhat egy virtuális gépet.

A virtuális gép létrehozásához adja hozzá ezt a kódot a fő metódus kipróbálási blokkjához:

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
> Ez az oktatóanyag a Windows Server operációs rendszer egy verzióját futtató virtuális gépet hoz létre. Ha többet szeretne tudni a többi lemezkép kiválasztásáról, olvassa el a [Navigálás és az Azure virtuálisgép-lemezképek kiválasztása a Windows PowerShell és az Azure CLI](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)használatával című témakört.
> 
>

Ha piactéri lemez lemezt szeretne használni piactéri lemezkép helyett, használja ezt a kódot: 

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

A virtuális gépek életciklusa során szükség lehet felügyeleti feladatok futtatására, például a virtuális gép indítására, leállítására vagy törlésére. Ezenkívül az ismétlődő vagy összetett feladatok automatizálására kód is létrehozható.

Ha valamit tennie kell a virtuális gép, be kell szereznie egy példányt. Adja hozzá ezt a kódot a fő metódus próbablokkjához:

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>Információ a virtuális gépről

A virtuális géppel kapcsolatos információk leéséhez adja hozzá ezt a kódot a fő metódus kipróbálási blokkjához:

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

Leállíthatja a virtuális gépet, és megtarthatja az összes beállítását, de továbbra is fel kell számítania, vagy leállíthatja a virtuális gépet, és felszabadíthatja azt. Amikor egy virtuális gép felvan szabadítva, a hozzá társított összes erőforrás is felszabadítva lesz, és a számlázás véget ér.

Ha meg szeretné állítani a virtuális gépet anélkül, hogy osztanád, add hozzá ezt a kódot a fő metódus próbablokkjához:

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

Ha fel szeretné osztani a virtuális gépet, módosítsa a PowerOff-hívást erre a kódra:

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>A virtuális gép elindítása

A virtuális gép elindításához adja hozzá ezt a kódot a fő metódus kipróbálási blokkjához:

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>A virtuális gép átméretezése

A virtuális gép méretének meghatározásakor a központi telepítés számos aspektusát figyelembe kell venni. További információ: [VM méretek](sizes.md).  

A virtuális gép méretének módosításához adja hozzá ezt a kódot a fő metódus kipróbálási blokkjához:

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>Adatlemez hozzáadása a virtuális géphez

Ha 2 GB méretű adatlemezt szeretne hozzáadni a virtuális géphez, 0 logikai egysége és egy írási segédforrása van, adja hozzá ezt a kódot a fő metódus próbablokkjához:

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>Erőforrások törlése

Mivel az Azure-ban használt erőforrásokért díjat számítunk fel, mindig ajánlott törölni azokat az erőforrásokat, amelyekre már nincs szükség. Ha törölni szeretné a virtuális gépeket és az összes támogató erőforrást, mindössze annyit kell tennie, hogy törli az erőforráscsoportot.

1. Az erőforráscsoport törléséhez adja hozzá ezt a kódot a fő metódus kipróbálási blokkjához:
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. Mentse az App.java fájlt.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Körülbelül öt percet vesz igénybe, amíg ez a konzolalkalmazás teljesen elindul az elejétől a végéig.

1. Az alkalmazás futtatásához használja ezt a Maven parancsot:

    ```
    mvn compile exec:java
    ```

2. Mielőtt az **Enter** billentyű lenyomásával megszeretné kezdeni az erőforrások törlését, eltarthat néhány percet az erőforrások létrehozásának ellenőrzése az Azure Portalon. Kattintson a központi telepítés állapotára a központi telepítéssel kapcsolatos információk megtekintéséhez.


## <a name="next-steps"></a>További lépések
* További információ a [Java-hoz való Azure-kódtárak](https://docs.microsoft.com/java/azure/java-sdk-azure-overview)használatáról.

