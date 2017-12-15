---
title: "Az Azure-ban első indításakor Linux virtuális gép testreszabása |} Microsoft Docs"
description: "Megtudhatja, hogyan használható felhőalapú inicializálás és Key Vault customze Linux virtuális gépek indulnak az Azure-ban először"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 83773e513ee2c92da733df05cd17dda2940a28cd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-customize-a-linux-virtual-machine-on-first-boot"></a>Az első rendszerindító Linux virtuális gépek testreszabása
Egy korábbi oktatóanyagban megtanulta, hogyan telepítse a virtuális gép (VM), és manuálisan SSH NGINX. Virtuális gépek gyors és egységes módon létrehozásához valamilyen automatizált művelettel általában van szükség. Általános gyakorlatként javasolt első indításakor a virtuális gépek testreszabása, hogy használja [felhő inicializálás](https://cloudinit.readthedocs.io). Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Felhő inicializálás konfigurációs fájl létrehozása
> * A felhő inicializálás fájlt használó virtuális gép létrehozása
> * Egy futó Node.js-alkalmazás megtekintése a virtuális gép létrehozása után
> * Key Vault használatával biztonságosan tárolni a tanúsítványokat
> * Automatizálhatja a biztonságos telepítéseket NGINX felhő inicializálás


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).  



## <a name="cloud-init-overview"></a>A cloud-init áttekintése
[Felhő inicializálás](https://cloudinit.readthedocs.io) Linux virtuális gép testreszabása, először elinduló széles körben használt módszer. Felhő inicializálás használhatja csomagok és a fájlok írási, vagy a felhasználók és biztonsági beállításainak. Felhő inicializálás során a rendszerindítási folyamat fut, mert nincsenek további lépéseket vagy a konfiguráció alkalmazásához szükséges ügynökök.

Felhő inicializálás terjesztéseket is használható. Például, hogy ne használjon **apt-get-telepítés** vagy **yum telepítése** csomag telepítéséhez. Helyette megadhatja a telepítendő csomagok listája. Felhő inicializálás automatikusan használja a natív csomag felügyeleti eszköz a distro választja.

A Microsoft a partnerekkel együttműködve az beszerzése felhő inicializálás tartalmazza, és működik-e az Azure biztosít lemezképeket a dolgozik. Az alábbi táblázat ismerteti a jelenlegi felhő inicializálás elérhetőségét Azure platformon lemezképek:

| Alias | Közzétevő | Ajánlat | SKU | Verzió |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-ES LTS VERZIÓ |legújabb |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |legújabb |
| CoreOS |CoreOS |CoreOS |Stable |legújabb |
| | OpenLogic | CentOS | 7-KONFIGURÁCIÓELEM | legújabb |
| | RedHat | RHEL | 7-NYERS-KONFIGURÁCIÓELEM | legújabb


## <a name="create-cloud-init-config-file"></a>Felhő inicializálás konfigurációs fájl létrehozása
Tekintse meg a műveletet a felhő inicializálás, hozzon létre, amely NGINX telepíthető és futtatható egy egyszerű "Hello World" Node.js-alkalmazás. A következő felhő inicializálás konfigurációs telepíti a szükséges csomagokat, létrehoz egy Node.js-alkalmazást, majd inicializálni és elindul az alkalmazás.

Hozzon létre egy fájlt az aktuális rendszerhéjban *felhő-init.txt* , majd illessze be a következő konfigurációt. A felhő rendszerhéj nem a helyi számítógépen hozzon létre például a fájlt. A szerkesztő kívánja használata. Adja meg `sensible-editor cloud-init.txt` hozza létre a fájlt, és elérhető szerkesztők listájának megtekintéséhez. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
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
  - path: /home/azureuser/myapp/index.js
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

Felhő inicializálás konfigurációs beállításokkal kapcsolatos további információkért lásd: [felhő inicializálás config példák](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
A virtuális gépek létrehozása előtt hozzon létre egy erőforráscsoportot, a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupAutomate* a a *eastus* helye:

```azurecli-interactive 
az group create --name myResourceGroupAutomate --location eastus
```

Most létrehozza a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#create). Használja a `--custom-data` paraméter felelt meg a felhő inicializálás konfigurációs fájlban. A teljes elérési útját adja meg a *felhő-init.txt* config Ha mentette a fájlt a jelenlegi munkakönyvtár kívül. Az alábbi példakód létrehozza a virtuális gépek nevű *myAutomatedVM*:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

A virtuális Gépet létrehozni, a csomagok telepítése és az alkalmazás elindításához néhány percet vesz igénybe. Nincsenek háttérfeladatok, hogy végezze el az Azure parancssori felület visszatér a parancssorba. Elképzelhető, hogy egy másik néhány percet, mielőtt hozzáférhetne az alkalmazáshoz. A virtuális gép létrehozása, tekintse meg a `publicIpAddress` megjeleníti azokat az Azure parancssori felület. Ezt a címet a webböngésző segítségével Node.js-alkalmazás eléréséhez használt.

A virtuális gép elérni kívánt webes forgalom engedélyezéséhez nyissa meg a port 80 az internetről a [az vm-port megnyitása](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myVM
```

## <a name="test-web-app"></a>Vizsgálati a webes alkalmazás
Most nyisson meg egy webböngészőt, és írja be *http://<publicIpAddress>*  a böngésző címsorába. Adja meg a saját nyilvános IP-címet a virtuális gép folyamatot létrehozni. A Node.js-alkalmazás az alábbi példában látható módon jelennek meg:

![Futó NGINX-hely megtekintése](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>A tanúsítványokat a Key Vault beszúrása
A választható szakasz bemutatja, hogyan lehet biztonságosan tanúsítványok tárolása az Azure Key Vault és szúrjon azokat a virtuális gép üzembe helyezése során. Ahelyett, hogy az egy egyéni lemezképet, amely tartalmazza a tanúsítványok bővíthetőség-a, ez a folyamat biztosítja, hogy a legfrissebb tanúsítványok vannak beszúrta egy virtuális géphez első indításakor. A folyamat során a tanúsítvány soha ne hagyja el az Azure platformon, vagy fel van fedve egy parancsfájl, a parancssori előzmények vagy a sablonban.

Az Azure Key Vault megvédi a titkosítási kulcsok és titkos kulcsokat, például a tanúsítványok vagy jelszavakat. Key Vault segít a egyszerűsíthető a kulcskezelési folyamatot, és lehetővé teszi a hozzáférést, és az adatok titkosításához használt kulcsok irányítást tarthat fenn. Ez a forgatókönyv bemutat néhány olyan Key Vault fogalmat történő létrehozásáról és használatáról a tanúsítvány, azonban nem Key Vault használatával teljes körű áttekintése.

A következő lépések bemutatják, hogyan zajlik:

- Egy Azure-tároló létrehozása
- Hozza létre, vagy a Key Vault tanúsítvány feltöltése
- Egy virtuális géphez a szúrjon tanúsítvány titkos kulcs létrehozása
- Hozzon létre egy virtuális Gépet, és a tanúsítvány beszúrása

### <a name="create-an-azure-key-vault"></a>Egy Azure-tároló létrehozása
Először hozzon létre egy rendelkező Key Vaultból [az keyvault létrehozása](/cli/azure/keyvault#create) és engedélyezi azt használja a virtuális gépek telepítésekor. Minden Key Vault szükséges egy egyedi nevet, és minden kisbetű kell lennie. Cserélje le *mykeyvault* az alábbi példában a saját egyedi kulcstároló nevével:

```azurecli-interactive 
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Tanúsítvány jön létre, és tárolja a Key Vault
Az éles környezetben való használathoz, importálnia kell a egy érvényes tanúsítvány megbízható-szolgáltató által aláírt [az keyvault tanúsítvány importálása](/cli/azure/keyvault/certificate#import). Ebben az oktatóanyagban a következő példa bemutatja, hogyan hozhat létre egy önaláírt tanúsítványt [az keyvault tanúsítvány létrehozása](/cli/azure/keyvault/certificate#create) , amely használja az alapértelmezett házirend:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Készítse elő a tanúsítvány használható a virtuális Géphez
A tanúsítvány használatára során a virtuális Gépet létrehozni folyamatot, a tanúsítvány az azonosítóhoz [az keyvault lista-verzióját](/cli/azure/keyvault/secret#list-versions). A virtuális gép kell bizonyos formátumát el azt a rendszerindító, így alakítsa át a tanúsítványt a tanúsítvány [az vm formátum-secret](/cli/azure/vm#format-secret). A következő példa a parancsok kimenetéből rendel változók könnyű használatra a következő lépésekben:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Felhő inicializálás config NGINX biztonságos létrehozása
Ha hoz létre egy virtuális Gépet, a tanúsítványok és kulcsok tárolása a a védett */var/lib/waagent/* könyvtár. A tanúsítvány felvétele a virtuális gép és konfigurálás NGINX automatizálása, egy frissített felhő inicializálás config az előző példából is használhat.

Hozzon létre egy fájlt *felhő-init-secured.txt* , majd illessze be a következő konfigurációt. Újra Ha a felhő rendszerhéj használata esetén hozzon létre a felhő inicializálás konfigurációs fájl vannak, és nem a helyi számítógépen. Használjon `sensible-editor cloud-init-secured.txt` hozza létre a fájlt, és elérhető szerkesztők listájának megtekintéséhez. Győződjön meg arról, hogy az egész felhő inicializálás fájl megfelelően lett lemásolva különösen az első sor:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
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
  - path: /home/azureuser/myapp/index.js
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
Most létrehozza a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#create). A tanúsítvány adatainak van beszúrta a rendelkező Key Vaultból a `--secrets` paraméter. Az előző példában szemléltetett is adja meg a felhő inicializálás config rendelkező a `--custom-data` paraméter:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

A virtuális Gépet létrehozni, a csomagok telepítése és az alkalmazás elindításához néhány percet vesz igénybe. Nincsenek háttérfeladatok, hogy végezze el az Azure parancssori felület visszatér a parancssorba. Elképzelhető, hogy egy másik néhány percet, mielőtt hozzáférhetne az alkalmazáshoz. A virtuális gép létrehozása, tekintse meg a `publicIpAddress` megjeleníti azokat az Azure parancssori felület. Ezt a címet a webböngésző segítségével Node.js-alkalmazás eléréséhez használt.

Biztonságos webes forgalom a virtuális gép eléréséhez engedélyezéséhez nyissa meg a port 443-as porton az internetről a [az vm-port megnyitása](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMSecured \
    --port 443
```

### <a name="test-secure-web-app"></a>Biztonságos webes alkalmazás tesztelése
Most nyisson meg egy webböngészőt, és írja be *https://<publicIpAddress>*  a böngésző címsorába. Adja meg a saját nyilvános IP-címet a virtuális gép folyamatot létrehozni. Fogadja el a biztonsági figyelmeztetést, ha önaláírt tanúsítványt használt:

![Fogadja el a webes böngésző biztonsági figyelmeztetés](./media/tutorial-automate-vm-deployment/browser-warning.png)

A biztonságos NGINX hely és a Node.js alkalmazás ezután megjelenik az alábbi példában látható módon:

![Futó biztonságos NGINX webhely megtekintése](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban a virtuális gépek a felhő inicializálás első rendszerindítás konfigurálta. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felhő inicializálás konfigurációs fájl létrehozása
> * A felhő inicializálás fájlt használó virtuális gép létrehozása
> * Egy futó Node.js-alkalmazás megtekintése a virtuális gép létrehozása után
> * Key Vault használatával biztonságosan tárolni a tanúsítványokat
> * Automatizálhatja a biztonságos telepítéseket NGINX felhő inicializálás

Továbblépés a következő oktatóanyag áttekintésével megismerheti, hogyan hozza létre egyéni Virtuálisgép-lemezképeket.

> [!div class="nextstepaction"]
> [Egyéni virtuálisgép-rendszerképek létrehozása](./tutorial-custom-images.md)
