<properties
    pageTitle="Linux virtuális gépek létrehozásának különböző módszerei | Microsoft Azure"
    description="Ez a cikk a Linux virtuális gépek Azure-ban való létrehozásának különböző módszereit ismerteti, és a további utasításokra mutató hivatkozásokat tartalmaz."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/12/2016"
    ms.author="iainfou"/>

# Linux virtuális gépek létrehozásának különböző módszerei a Resource Manager használatával

Az Azure különböző módszereket kínál a virtuális gépek létrehozásához a Resource Manager üzembe helyezési modelljével a különböző felhasználóknak és céloknak megfelelően. Ez a cikk ezeket a különböző módszereket, illetve a Linux virtuális gépek létrehozásához választható lehetőségeket összegzi.

## Választható eszközök

### Parancssori felület: Azure parancssori felület 

A parancssori felületről használja az Azure parancssori felületét. További információ az [Azure parancssori felület telepítésével](../xplat-cli-install.md) kapcsolatban npm, Docker-tároló vagy telepítési parancsfájl használatával. Az alábbi oktatóanyagok az Azure parancssori felület használatával kapcsolatos példákat biztosítanak:

* [Linux virtuális gép létrehozása az Azure parancssori felületen fejlesztéshez és teszteléshez](virtual-machines-linux-quick-create-cli.md) 

* [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Create a Linux VM from the ground up using the Azure CLI (Linux virtuális gép létrehozása az alapoktól az Azure parancssori felület használatával)](virtual-machines-linux-create-cli-complete.md)

### Grafikus felhasználói felület: Azure portál

Az [Azure portál](https://portal.azure.com) grafikus felhasználói felülete egyszerű módszert biztosít a virtuális gépek kipróbálására, különösen, ha nemrég kezdte meg az Azure használatát, mert semmit nem kell telepítenie a rendszerre. A virtuális gép létrehozása az Azure portállal:

* [Linux rendszert futtató virtuális gép létrehozása az Azure portállal](virtual-machines-linux-quick-create-portal.md) 

## Választható operációs rendszerek és rendszerképek

Mindkét módszer esetén egy rendszerkép választ ki a futtatni kívánt operációs rendszer alapján. Az Azure és a partnerei számos rendszerképet kínálnak, amelyek némelyike előre telepített alkalmazásokat és eszközöket tartalmaz. Feltöltheti az egyik saját rendszerképét is.

### Azure-rendszerképek

A fenti cikkekben foglaltak végrehajtása során egyszerűen használhat meglévő Azure-rendszerképet virtuális gép létrehozásához, és annak testreszabásához a hálózat, terheléselosztás és egyebek számára. A portál segítségével az Azure Piactéren érheti el az Azure által szállított rendszerképeket. Hasonló listákat szerezhet be a parancssorral. Az Azure parancssori felületen például futtassa az `azure vm image list` parancsot az összes elérhető rendszerkép listájának lekéréséhez hely és közzétevő alapján. Az elérhető rendszerképek tallózásával és használatával kapcsolatos példák: [Navigate and select Azure virtual machine images with the Azure CLI](virtual-machines-linux-cli-ps-findimage.md) (Azure virtuális gépek rendszerképének keresése és kiválasztása az Azure parancssori felülettel).

### Saját rendszerkép használata

Ha speciális egyéni beállításokra van szüksége, meglévő Azure virtuális gépen alapuló rendszerképet használhat a virtuális gép *rögzítésével*, vagy saját, virtuális merevlemezen (VHD-n) tárolt rendszerkép feltöltésével. A támogatott disztribúciókkal és a saját rendszerképek használatával kapcsolatban az alábbi cikkekben tekinthet meg további információt:

* [Azure által támogatott disztribúciók](virtual-machines-linux-endorsed-distros.md)

* [Nem támogatott disztribúciókkal kapcsolatos tudnivalók](virtual-machines-linux-create-upload-generic.md)

* [How to capture a Linux virtual machine as a Resource Manager template](virtual-machines-linux-capture-image.md) (Linux virtuális gép rögzítése Resource Manager-sablonként). 

## További lépések

* Próbálja ki az oktatóprogramok valamelyikét Linux virtuális gép létrehozásához a [portálon](virtual-machines-linux-quick-create-portal.md), a [parancssori felülettel](virtual-machines-linux-quick-create-cli.md) vagy Azure Resource Manager-[sablonnal](virtual-machines-linux-cli-deploy-templates.md).

* Linux virtuális gép létrehozása után egyszerűen [adhat hozzá adatlemezt](virtual-machines-linux-add-disk.md).

* Gyors lépések [jelszó vagy SSH-kulcsok alaphelyzetbe állításához és felhasználók kezeléséhez](virtual-machines-linux-using-vmaccess-extension.md)



<!--HONumber=Jun16_HO2--->


