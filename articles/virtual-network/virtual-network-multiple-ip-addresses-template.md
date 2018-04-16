---
title: "Az Azure virtuális gépek - sablon több IP-cím |} Microsoft Docs"
description: "Útmutató több IP-címek hozzárendelése a virtuális gép Azure Resource Manager-sablonnal."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Több IP-címek hozzárendelése a virtuális gépek Azure Resource Manager-sablonnal

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk ismerteti (VM) virtuális gép létrehozása az Azure Resource Manager deployment használatával Resource Manager-sablon használatával. Több nyilvános és magánhálózati IP-cím nem lehet hozzárendelni az azonos hálózati adapterre, a klasszikus üzembe helyezési modell használatával egy virtuális gép telepítésekor. Az Azure üzembe helyezési modellel kapcsolatos további tudnivalókért olvassa el a [üzembe helyezési modellel megértéséhez](../resource-manager-deployment-model.md) cikk.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Sablon leírása

A sablonok telepítésével lehetővé teszi gyorsan és következetesen legyenek különböző konfigurációs értékekkel létre Azure-erőforrások. Olvassa el a [Resource Manager sablonokhoz](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a következő cikket: Ha még nem ismeri a Azure Resource Manager-sablonok. A [több IP-címekkel rendelkező olyan virtuális gép telepítéséhez](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) sablon használata ebben a cikkben.

<a name="resources"></a>A sablon telepítése hoz létre a következő erőforrásokat:

|Erőforrás|Név|Leírás|
|---|---|---|
|Hálózati illesztő|*myNic1*|Ez a cikk a forgatókönyv szakaszban leírt három IP-konfigurációk létrehozása és hozzárendelése a hálózati adapternek.|
|Nyilvános IP-cím erőforrás|2 jönnek létre: *myPublicIP* és *myPublicIP2*|Ezeket az erőforrásokat statikus nyilvános IP-címek, és vannak rendelve a *IPConfig-1* és *IPConfig-2* IP-konfigurációk a forgatókönyvet ismerteti.|
|VM|*myVM1*|Egy szabványos DS3 virtuális gép.|
|Virtuális hálózat|*myVNet1*|Egy virtuális hálózatot egyetlen alhálózattal nevű *mySubnet*.|
|Tárfiók|Egyedi, a központi telepítéshez|A storage-fiók.|

<a name="parameters"></a>A sablon telepítésekor meg kell adnia a következő paraméter értékét:

|Név|Leírás|
|---|---|
|adminUsername|Rendszergazda felhasználóneve. A felhasználónévnek meg kell felelnie [Azure username követelmények](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Rendszergazdai jelszó a jelszót meg kell felelnie [Azure jelszavakra vonatkozó követelmények](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|PublicIPAddressName1 DNS-nevét. A DNS-nevét feloldja egy, a nyilvános IP-címek a virtuális Géphez rendelt. A név az Azure-régió (hely) a virtuális gép létrehozása belül egyedinek kell lennie.|
|dnsLabelPrefix1|PublicIPAddressName2 DNS-nevét. A DNS-nevét feloldja egy, a nyilvános IP-címek a virtuális Géphez rendelt. A név az Azure-régió (hely) a virtuális gép létrehozása belül egyedinek kell lennie.|
|OSVersion|A virtuális gép windowsos/Linuxos verzióját. Az operációs rendszer a megadott Windows/Linux-verzió kijelölt teljesen javított képe.|
|imagePublisher|A Windows/Linux kép közzétevőjének, a kiválasztott virtuális géphez.|
|imageOffer|A kiválasztott virtuális géphez Windows/Linux kép.|

A sablon által telepített erőforrások mindegyikének több alapértelmezett beállításokkal van konfigurálva. Ezek a beállítások az alábbi módszerekkel keresztül tekintheti meg:

- **A sablon megtekintése a Githubon:** Ha jártas a sablonok, megtekintheti az beállításairól a [sablon](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Telepítés után a beállítások megtekintéséhez:** Ha még nem ismeri a sablonok, a sablon a következő szakaszokban lévő lépéseket követve telepítheti, és nézze meg a beállítások központi telepítés után.

A sablon telepítéséhez használható az Azure-portálon, PowerShell vagy az Azure parancssori felület (CLI). Összes módszer ugyanazt az eredményt eredményez. A sablon telepítéséhez hajtsa végre az alábbi szakaszokban lépéseit:

## <a name="deploy-using-the-azure-portal"></a>Telepítheti az Azure portál használatával

A sablon az Azure portál használatával történő üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Ha szükséges, módosítsa a sablon. A sablon telepíti az erőforrások és beállítások szerepel a [erőforrások](#resources) című szakaszát. Sablonok és ahhoz, hogy azok hogyan kapcsolatos további tudnivalókért olvassa el a [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)cikk.
2. A sablon telepítéséhez az alábbi módszerek egyikével:
    - **Válassza ki a sablon a portálon:** lépéseinek végrehajtásához a [egyéni sablont az erőforrások telepítése](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) cikk. Válassza ki a korábban létrehozott sablon *101-vm – több-ipconfig*.
    - **Közvetlenül:** a következő gombra kattintva nyissa meg a sablon közvetlenül a portálon:<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Akármelyik módszert választja, adja meg az értékeket kell a [paraméterek](#parameters) ebben a cikkben korábban felsorolt. A virtuális gép telepítése után csatlakoztassa a virtuális Gépet, és a privát IP-címek hozzáadása az operációs rendszer, a lépések végrehajtásával telepítette a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegyen fel a nyilvános IP-címeket az operációs rendszer.

## <a name="deploy-using-powershell"></a>Telepítheti a PowerShell használatával

A sablon PowerShell használatával történő üzembe helyezéséhez kövesse az alábbi lépéseket:

1. A sablon telepítéséhez a lépések végrehajtásával a [a PowerShell-lel egy sablon telepítésének](../azure-resource-manager/resource-group-template-deploy-cli.md) cikk. A cikk ismerteti a sablonok telepítésével többféle módon. Ha úgy dönt, hogy szabályzatsablonokat a `-TemplateUri parameter`, a sablonnak a megadott URI azonosító *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Ha úgy dönt, hogy szabályzatsablonokat a `-TemplateFile` paraméter, a tartalom másolása a [sablonfájl](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) a Githubból a számítógépen egy új fájlba. Ha szükséges, módosítsa a sablon tartalmát. A sablon telepíti az erőforrások és beállítások szerepel a [erőforrások](#resources) című szakaszát. Sablonok és ahhoz, hogy azok hogyan kapcsolatos további tudnivalókért olvassa el a [Azure Resource Manager-sablonok készítése ](../azure-resource-manager/resource-group-authoring-templates.md)cikk.

    A beállítás úgy dönt, hogy a sablon üzembe helyezése, függetlenül meg kell adnia a felsorolt paraméter értékei a [paraméterek](#parameters) című szakaszát. Adja meg a paramétereket a paraméterfájl használata mellett dönt, ha a tartalom másolása a [paraméterfájl](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) a Githubból a számítógépen egy új fájlba. Módosítsa az értékeket a fájlban. Használja ki a létrehozott fájlt értéke a `-TemplateParameterFile` paraméter.

    Érvényes értékek meghatározásáról az OSVersion, ImagePublisher és imageOffer paraméterek, lépéseinek végrehajtásához a [keresse meg és jelölje be a Windows virtuális gép képek cikk](../virtual-machines/windows/cli-ps-findimage.md) cikk.

    >[!TIP]
    >Ha nem biztos abban, hogy rendelkezésre áll-e egy dnslabelprefix, adja meg a `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` parancs megállapítása. Ha rendelkezésre áll, a parancs visszaadható `True`.

2. A virtuális gép telepítése után csatlakoztassa a virtuális Gépet, és a privát IP-címek hozzáadása az operációs rendszer, a lépések végrehajtásával telepítette a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegyen fel a nyilvános IP-címeket az operációs rendszer.

## <a name="deploy-using-the-azure-cli"></a>Telepítheti az Azure parancssori felület használatával

A sablon az Azure CLI 1.0 használatával történő üzembe helyezéséhez kövesse az alábbi lépéseket:

1. A sablon telepítéséhez a lépések végrehajtásával a [központi telepítése az Azure parancssori Felülettel rendelkező sablont](../azure-resource-manager/resource-group-template-deploy-cli.md) cikk. A cikk a sablon telepítéséhez több beállításokat ismerteti. Ha úgy dönt, hogy szabályzatsablonokat a `--template-uri` (-f), a sablonnak a megadott URI azonosító *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Ha úgy dönt, hogy szabályzatsablonokat a `--template-file` (-f) paraméter, a tartalom másolása a [sablonfájl](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) a Githubból a számítógépen egy új fájlba. Ha szükséges, módosítsa a sablon tartalmát. A sablon telepíti az erőforrások és beállítások szerepel a [erőforrások](#resources) című szakaszát. Sablonok és ahhoz, hogy azok hogyan kapcsolatos további tudnivalókért olvassa el a [Azure Resource Manager-sablonok készítése ](../azure-resource-manager/resource-group-authoring-templates.md)cikk.

    A beállítás úgy dönt, hogy a sablon üzembe helyezése, függetlenül meg kell adnia a felsorolt paraméter értékei a [paraméterek](#parameters) című szakaszát. Adja meg a paramétereket a paraméterfájl használata mellett dönt, ha a tartalom másolása a [paraméterfájl](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) a Githubból a számítógépen egy új fájlba. Módosítsa az értékeket a fájlban. Használja ki a létrehozott fájlt értéke a `--parameters-file` (-e) paraméter.

    Érvényes értékek meghatározásáról az OSVersion, ImagePublisher és imageOffer paraméterek, lépéseinek végrehajtásához a [keresse meg és jelölje be a Windows virtuális gép képek cikk](../virtual-machines/windows/cli-ps-findimage.md) cikk.

2. A virtuális gép telepítése után csatlakoztassa a virtuális Gépet, és a privát IP-címek hozzáadása az operációs rendszer, a lépések végrehajtásával telepítette a [hozzáadása IP-címek egy virtuális gép operációs rendszerre](#os-config) című szakaszát. Ne vegyen fel a nyilvános IP-címeket az operációs rendszer.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
