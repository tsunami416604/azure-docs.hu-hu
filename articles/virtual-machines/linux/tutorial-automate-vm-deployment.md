---
title: Oktatóanyag – Linux rendszerű virtuális gép testreszabása a Cloud-init használatával az Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Cloud-init és a Key Vaultt a linuxos virtuális gépek testreszabásához az Azure-ban való első rendszerindítás alkalmával
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 27c7e32f081003ac236c6d1405eb3512f6c4433c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034642"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Oktatóanyag – Cloud-init használata az Azure-ban először induló linuxos virtuális gépek testreszabásához

Egy korábbi oktatóanyagból megtudhatta, hogyan csatlakozhat SSH-val a virtuális gépekhez, és hogyan telepítheti manuálisan az NGINX-et. A virtuális gépek gyors és következetes létrehozásához általában valamilyen automatizálásra van szükség. A virtuális gépek első rendszerindításkor való testreszabásának általánosan használt megközelítése a [cloud-init](https://cloudinit.readthedocs.io) használata. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Cloud-init konfigurációs fájl létrehozása
> * Cloud-init-fájlt használó virtuális gép létrehozása
> * Futó Node.js-alkalmazás megtekintése a virtuális gép létrehozása után
> * A Key Vault használata a tanúsítványok biztonságos tárolására
> * Az NGINX biztonságos telepítéseinek automatizálása a cloud-init használatával

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="cloud-init-overview"></a>A cloud-init áttekintése
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a cloud-init a kezdeti rendszerindítás során fut, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

A partnereinkkel dolgozunk rajta, hogy egyre több általuk biztosított Azure-rendszerkép tartalmazza a cloud-init eszközt. Az alábbi táblázat a cloud-init jelenlegi elérhetőségét ismerteti az Azure-platformrendszerképeken:

| Közzétevő | Ajánlat | SKU | Verzió | a cloud-init kész |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |Legújabb |igen | 
|Canonical |UbuntuServer |16.04-LTS |Legújabb |igen | 
|Canonical |UbuntuServer |14.04.5-LTS |Legújabb |igen |
|CoreOS |CoreOS |Stable |Legújabb |igen |
|OpenLogic 7,6 |CentOS |7-CI |Legújabb |előzetes verzió |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |igen |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |előzetes verzió |


## <a name="create-cloud-init-config-file"></a>Cloud-init konfigurációs fájl létrehozása
A cloud-init működés közbeni megtekintéséhez hozzon létre egy virtuális gépet, amely telepíti az NGINX-et, és egy egyszerű „Hello World” Node.js-alkalmazást futtat. Az alábbi cloud-init konfiguráció telepíti a szükséges csomagokat, létrehoz egy Node.js-alkalmazást, majd inicializálja és elindítja azt.

A bash-parancssorban vagy a Cloud Shell hozzon létre egy *Cloud-init. txt* nevű fájlt, és illessze be a következő konfigurációt. Írja be például a következőt: `sensible-editor cloud-init.txt` a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```azurecli-interactive
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

További információk a cloud-init konfigurációs beállításairól: [cloud-init példakonfigurációk](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
Mielőtt létrehozhatna egy virtuális gépet, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. A következő példa egy *myResourceGroupAutomate* nevű erőforráscsoportot hoz létre az *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

Most hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

A virtuális gép létrehozása, a csomagok telepítése és az alkalmazás elindítása néhány percig tart. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. Eltarthat még néhány percig, amíg hozzáférhet az alkalmazáshoz. A virtuális gép létrehozása után jegyezze fel az Azure CLI által megjelenített `publicIpAddress` értéket. Ezzel a címmel érheti el a Node.js-alkalmazást a webböngészőn keresztül.

Ahhoz, hogy a webes adatforgalom elérje a virtuális gépét, nyissa meg az internetről a 80-as portot az [az vm open-port](/cli/azure/vm#az-vm-open-port) paranccsal:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>Webalkalmazás tesztelése
Most nyisson meg egy webböngészőt, és írja be a következőt *: http:\/\/\<publicIpAddress >* a címsorban. Adja meg a saját nyilvános IP-címét, amelyet a virtuális gép létrehozásakor kapott. A Node.js-alkalmazás a következő példához hasonlóan jelenik meg:

![Futó NGINX-webhely megtekintése](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Tanúsítványok beszúrása a Key Vaultból
Ez az opcionális szakasz bemutatja, hogyan tárolhatja biztonságosan a tanúsítványokat az Azure Key Vaultban, és hogyan szúrhatja be azokat a virtuális gép üzembe helyezése során. A beépített tanúsítványokat tartalmazó egyéni rendszerképek használata helyett ez a folyamat biztosítja, hogy a legnaprakészebb tanúsítványok legyenek beszúrva a virtuális gépbe az első rendszerindításkor. A folyamat során a tanúsítvány egyszer sem hagyja el az Azure platformot, és nem jelenik meg a szkriptekben, a parancssori előzményekben és a sablonokban.

Az Azure Key Vault megvédi a titkosítási kulcsokat és titkos kódokat, például a tanúsítványokat és jelszavakat. A Key Vault leegyszerűsíti a kulcskezelési folyamatot, valamint lehetővé teszi az adatok titkosításához használt kulcsok feletti teljes körű felügyeletet. Ez a forgatókönyv bemutat néhány, a tanúsítványok létrehozásával és használatával kapcsolatos Key Vault-fogalmat, viszont nem nyújt teljes körű áttekintést a Key Vault használatáról.

A következő lépések bemutatják, hogyan végezheti el a következőket:

- Azure Key Vault létrehozása;
- tanúsítvány létrehozása vagy feltöltése a Key Vaultba;
- Titkos kulcs létrehozása a tanúsítványból, majd beszúrása a virtuális gépbe
- Virtuális gép létrehozása, és a tanúsítvány beszúrása

### <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;
Először hozzon létre egy Key Vaultot az [az keyvault create](/cli/azure/keyvault#az-keyvault-create) paranccsal, és engedélyezze a használatát a virtuális gépek üzembe helyezésekor. Mindegyik Key Vaultnak egyedi névvel kell rendelkeznie, amely csak kisbetűkből állhat. Cserélje le a *mykeyvault* nevet a következő példában a saját egyedi Key Vault-névre:

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Tanúsítvány létrehozása és tárolása a Key Vaultban
Éles környezetben importálnia kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt az [az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) paranccsal. Ebben az oktatóanyagban a következő példa mutatja be, hogyan hozhat létre olyan önaláírt tanúsítványt az [az keyvault certificate create](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) paranccsal, amely az alapértelmezett tanúsítványszabályzatot használja:

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Tanúsítvány előkészítése virtuális géppel való használatra
Ha a virtuális gép létrehozása alatt szeretné használni a tanúsítványt, szerezze be a tanúsítvány azonosítóját az [az keyvault secret list-versions](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions) paranccsal. A virtuális gép csak egy bizonyos formátumú tanúsítványt képes beszúrni a rendszerindításkor, ezért alakítsa át a tanúsítványt az [az vm secret format](/cli/azure/vm) paranccsal. A következő példa ezen parancsok kimenetét ezekhez változókhoz rendeli, hogy könnyen használhatók legyenek a következő lépésekben:

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Cloud-init konfiguráció létrehozása az NGINX védelméhez
Virtuális gép létrehozásakor a tanúsítványokat és a kulcsokat a védett */var/lib/waagent/* könyvtár tárolja. A tanúsítvány virtuális géphez való hozzáadásának automatizálásához és az NGINX konfigurálásához használhat egy frissített cloud-init konfigurációt az előző példából.

Hozzon létre egy *cloud-init-secured.txt* nevű fájlt, és illessze be a következő konfigurációt. Ha a Cloud Shell használja, hozza létre a Cloud-init konfigurációs fájlt, és ne a helyi gépen. Írja be például a következőt: `sensible-editor cloud-init-secured.txt` a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Biztonságos virtuális gép létrehozása
Most hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az-vm-create) paranccsal. A tanúsítványadatokat a `--secrets` paraméterrel szúrhatja be a Key Vaultból. A cloud-init konfigurációt az előző példához hasonlóan itt is a `--custom-data` paraméterrel adhatja meg:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

A virtuális gép létrehozása, a csomagok telepítése és az alkalmazás elindítása néhány percig tart. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. Eltarthat még néhány percig, amíg hozzáférhet az alkalmazáshoz. A virtuális gép létrehozása után jegyezze fel az Azure CLI által megjelenített `publicIpAddress` értéket. Ezzel a címmel érheti el a Node.js-alkalmazást a webböngészőn keresztül.

Ahhoz, hogy a biztonságos webes adatforgalom elérje a virtuális gépét, nyissa meg az internetről a 443-as portot az [az vm open-port](/cli/azure/vm#az-vm-open-port) paranccsal:

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>A biztonságos webalkalmazás tesztelése
Most nyisson meg egy webböngészőt, és írja be a *https:\/\/\<publicIpAddress >* a címsorba. Adja meg a saját nyilvános IP-címét, amelyet az előző virtuálisgép-létrehozási folyamat kimeneteként kapott. Fogadja el a biztonsági figyelmeztetést, ha önaláírt tanúsítványt használt:

![Webböngésző biztonsági figyelmeztetésének elfogadása](./media/tutorial-automate-vm-deployment/browser-warning.png)

Ekkor a biztonságos NGINX-webhely és a Node.js-alkalmazás a következő példához hasonlóan jelennek meg:

![Futó biztonságos NGINX-webhely megtekintése](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban virtuális gépeket konfigurált az első rendszerindításkor a cloud-init használatával. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Cloud-init konfigurációs fájl létrehozása
> * Cloud-init-fájlt használó virtuális gép létrehozása
> * Futó Node.js-alkalmazás megtekintése a virtuális gép létrehozása után
> * A Key Vault használata a tanúsítványok biztonságos tárolására
> * Az NGINX biztonságos telepítéseinek automatizálása a cloud-init használatával

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre egyéni virtuálisgép-rendszerképeket.

> [!div class="nextstepaction"]
> [Egyéni virtuálisgép-rendszerképek létrehozása](./tutorial-custom-images.md)
