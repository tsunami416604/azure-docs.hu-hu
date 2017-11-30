---
title: "Egy SSL-tanúsítványok Azure web server Secure |} Microsoft Docs"
description: "Megtudhatja, mennyire biztonságos a NGINX webkiszolgáló SSL-tanúsítványokkal a Linux virtuális gép az Azure-ban"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/17/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d2d6a0b00704e1d97be9a4c5bd00ba37374419e5
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="secure-a-web-server-with-ssl-certificates-on-a-linux-virtual-machine-in-azure"></a>Biztonságos Linux virtuális gépre az Azure-ban SSL-tanúsítványokkal egy webkiszolgáló
Biztonságos webkiszolgálók, a Secure Sockets később (SSL) tanúsítvány használható webes forgalom titkosításához. E SSL-tanúsítványokat tárolhatja az Azure Key Vault, és a Linux virtuális gépek (VM) tanúsítványok biztonságos telepítéshez engedélyezése az Azure-ban. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egy Azure-tároló létrehozása
> * Hozza létre, vagy a Key Vault tanúsítvány feltöltése
> * Hozzon létre egy virtuális Gépet, és az NGINX-webkiszolgáló telepítése
> * A tanúsítvány szúrjon be a virtuális gép, és SSL-kötést NGINX konfigurálása

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).  


## <a name="overview"></a>Áttekintés
Az Azure Key Vault megvédi a titkosítási kulcsok és titkos kulcsok, ilyen tanúsítványokat vagy jelszavakat. Key Vault segít a felügyeleti folyamat egyszerűsítésére, és lehetővé teszi a kulcsok ezeknek a tanúsítványoknak elérő irányítást tarthat fenn. Hozzon létre egy önaláírt tanúsítványt Key Vault belül, vagy töltsön fel egy meglévő, a megbízható tanúsítványt, amely már Ön a tulajdonosa.

Ahelyett, hogy az egyéni Virtuálisgép-lemezkép tanúsítványokat tartalmaz bővíthetőség a tanúsítványok behelyezése egy futó virtuális Gépet. Ez a folyamat biztosítja, hogy a legfrissebb tanúsítványok telepítve vannak a webkiszolgáló üzembe helyezése során. Újítsa meg, vagy cserélje le a tanúsítványt, ha nem is kell létrehoznia egy új egyéni Virtuálisgép-lemezkép. A legújabb tanúsítványok automatikusan vannak olyanok, további virtuális gépek létrehozásakor. A teljes folyamat során a tanúsítványok soha ne hagyja meg az Azure platformon, vagy egy parancsfájl, a parancssori előzmények vagy a sablon ki vannak téve.


## <a name="create-an-azure-key-vault"></a>Egy Azure-tároló létrehozása
Tanúsítványok és a kulcstároló létrehozásához, hozzon létre egy erőforráscsoportot, a [az csoport létrehozása](/cli/azure/group#create). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroupSecureWeb* a a *eastus* helye:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Ezután hozzon létre egy rendelkező Key Vaultból [az keyvault létrehozása](/cli/azure/keyvault#create) és engedélyezi azt használja a virtuális gépek telepítésekor. Minden Key Vault szükséges egy egyedi nevet, és minden kisbetű kell lennie. Cserélje le  *<mykeyvault>*  az alábbi példában a saját egyedi kulcstároló nevével:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Egy tanúsítvány jön létre, és tárolja a Key Vault
Az éles környezetben való használathoz, importálnia kell a egy érvényes tanúsítvány megbízható-szolgáltató által aláírt [az keyvault tanúsítvány importálása](/cli/azure/keyvault/certificate#az_keyvault_certificate_import). Ebben az oktatóanyagban a következő példa bemutatja, hogyan hozhat létre egy önaláírt tanúsítványt [az keyvault tanúsítvány létrehozása](/cli/azure/keyvault/certificate#az_keyvault_certificate_create) , amely használja az alapértelmezett házirend:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>Készítse elő a tanúsítványt egy virtuális Géphez való használatra
A tanúsítvány használatára során a virtuális Gépet létrehozni folyamatot, a tanúsítvány az azonosítóhoz [az keyvault lista-verzióját](/cli/azure/keyvault/secret#list-versions). Alakítsa át a tanúsítványt, amelynek [az vm formátum-secret](/cli/azure/vm#format-secret). A következő példa a parancsok kimenetéből rendel változók könnyű használatra a következő lépésekben:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm format-secret --secret "$secret")
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>Hozzon létre egy felhő-init config NGINX védelméhez
[Felhő inicializálás](https://cloudinit.readthedocs.io) Linux virtuális gép testreszabása, először elinduló széles körben használt módszer. Felhő inicializálás használhatja csomagok és a fájlok írási, vagy a felhasználók és biztonsági beállításainak. Felhő inicializálás során a rendszerindítási folyamat fut, mert nincsenek további lépéseket vagy a konfiguráció alkalmazásához szükséges ügynökök.

Ha hoz létre egy virtuális Gépet, a tanúsítványok és kulcsok tárolása a a védett */var/lib/waagent/* könyvtár. A tanúsítvány felvétele a virtuális gép és a webkiszolgáló konfigurálása automatizálását, használja a felhő inicializálás. Ebben a példában azt telepítse és konfigurálja a NGINX webkiszolgáló. Telepítése és konfigurálása az Apache használhatja ugyanazt a folyamatot. 

Hozzon létre egy fájlt *felhő-init-webalkalmazás-server.txt* , majd illessze be a következő konfigurációt:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Biztonságos virtuális gép létrehozása
Most létrehozza a virtuális gép és [az virtuális gép létrehozása](/cli/azure/vm#create). A tanúsítvány adatainak van beszúrta a rendelkező Key Vaultból a `--secrets` paraméter. Adja meg a felhő inicializálás config rendelkező a `--custom-data` paraméter:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

A virtuális Gépet létrehozni, a csomagok telepítése és az alkalmazás elindításához néhány percet vesz igénybe. A virtuális gép létrehozása, tekintse meg a `publicIpAddress` megjeleníti azokat az Azure parancssori felület. Ezt a címet egy webböngészőben a webhely eléréséhez használt.

Biztonságos webes forgalom a virtuális gép eléréséhez engedélyezéséhez nyissa meg a port 443-as porton az internetről a [az vm-port megnyitása](/cli/azure/vm#open-port):

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Biztonságos webes alkalmazás tesztelése
Most nyisson meg egy webböngészőt, és írja be *https://<publicIpAddress>*  a böngésző címsorába. Adja meg a saját nyilvános IP-címet a virtuális gép folyamatot létrehozni. Fogadja el a biztonsági figyelmeztetést, ha önaláírt tanúsítványt használt:

![Fogadja el a webes böngésző biztonsági figyelmeztetés](./media/tutorial-secure-web-server/browser-warning.png)

A biztonságos NGINX-webhelyet akkor jelenik meg, az alábbi példában látható módon:

![Futó biztonságos NGINX webhely megtekintése](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az NGINX webkiszolgáló az Azure Key Vault tárolt SSL-tanúsítvánnyal védett. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Egy Azure-tároló létrehozása
> * Hozza létre, vagy a Key Vault tanúsítvány feltöltése
> * Hozzon létre egy virtuális Gépet, és az NGINX-webkiszolgáló telepítése
> * A tanúsítvány szúrjon be a virtuális gép, és SSL-kötést NGINX konfigurálása

Kövesse a hivatkozásra kattintva megtekintheti az előre elkészített virtuálisgép-parancsprogram minták.

> [!div class="nextstepaction"]
> [Windows virtuális gép parancsfájl minták](./cli-samples.md)