---
title: "Az Azure-ban fejlesztési virtuális gépen ElasticSearch telepítése"
description: "Az oktatóanyag - telepítés a rugalmas készlet alakzatot Linux virtuális gép az Azure-ban fejlesztési"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: 5b0b51504478cc0d501a89760ccd60808a69ccbd
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>A rugalmas készlet telepítése egy Azure virtuális gépen

Ez a cikk bemutatja, hogyan telepítése [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash), és [Kibana](https://www.elastic.co/products/kibana), az Ubuntu virtuális gép az Azure-ban. A rugalmas tartozó művelet megtekintéséhez opcionálisan is Kibana csatlakozhat, és a naplózási adatok egy minta dolgozni. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Ubuntu virtuális gép létrehozása az Azure erőforrás-csoportban
> * Elasticsearch Logstash és Kibana telepítése a virtuális gépen
> * A minta adatokat küldeni a Logstash Elasticsearch 
> * Nyissa meg a portok és a Kibana konzolon adatok használata


 A központi telepítés alkalmas alapvető fejlesztése a a rugalmas készlet. A rugalmas verem, beleértve az éles környezetbe, ajánlásokat bővebben lásd: a [rugalmas dokumentáció](https://www.elastic.co/guide/index.html) és a [Azure architektúra Center](/azure/architecture/elasticsearch/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.4 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#create) paranccsal. 

Az alábbi példa egy *myVM* nevű virtuális gépet és SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapméretezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>Bejelentkezés a virtuális gépre SSH-val

Ha a nyilvános IP-címet a virtuális gép már nem tudja, futtassa a [az nyilvános ip-lista](/cli/azure/network/public-ip#list) parancs:

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Helyettesítse be a megfelelő nyilvános IP-címet a virtuális gép. Ebben a példában az IP-cím van *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>A rugalmas készlet telepítése

A Elasticsearch aláírási kulcs importálása, és a rugalmas csomag összetevőtárházat tartalmazza APT adatforrások listája frissítése:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

A virtuális Gépen a Java virtuális telepítse és konfigurálja a változó a JAVA_HOME esetén szükség a rugalmas készlet összetevők futtatásához.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Ubuntu csomag adatforrások frissítése és Elasticsearch, Kibana és Logstash telepítése a következő parancsok futtatásával.

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Részletes telepítési utasításokat, beleértve a directory elrendezések és a kezdeti konfigurációs karbantartása [rugalmas tartozó dokumentáció](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html)

## <a name="start-elasticsearch"></a>Indítsa el a Elasticsearch 

Indítsa el a Elasticsearch a virtuális gépen a következő paranccsal:

```bash
sudo systemctl start elasticsearch.service
```

Ez a parancs nem kimenetet hoz létre, ezért ellenőrizze, hogy Elasticsearch ennek a virtuális gép fut-e `curl` parancs:

```bash
curl -XGET 'localhost:9200/'
```

Ha Elasticsearch fut, a következőhöz hasonló kimenetnek jelenik meg:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Indítsa el a Logstash és Elasticsearch adatok hozzáadása

Indítsa el a Logstash a következő paranccsal:

```bash 
sudo systemctl start logstash.service
```

Próbálja Logstash győződjön meg arról, hogy helyesen működik interaktív módban:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Ez az alapszintű logstash [csővezeték](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html) , amely echók szabványos bemeneti normál a kimenetbe. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

A kernel-üzenetek továbbítása a virtuális gép Elasticsearch Logstash beállítása. Hozzon létre egy új fájlt egy üres nevű Directory `vm-syslog-logstash.conf` és illessze be a következő Logstash konfigurációt:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Ez a konfiguráció tesztelése, és a syslog-adatot küldeni Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

A rendszernapló-bejegyzések a terminálon annál a Elasticsearch küldött látni. Használjon `CTRL+C` kattintva lépjen ki a Logstash, amennyiben az elküldött adatokat.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Indítsa el a Kibana és Elasticsearch adatok megjelenítéséhez

Szerkesztés `/etc/kibana/kibana.yml` , és módosítsa az IP-cím Kibana figyeli, hogy hozzáférhessen a webböngészőben.

```bash
server.host:"0.0.0.0"
```

Indítsa el a Kibana a következő paranccsal:

```bash
sudo systemctl start kibana.service
```

Nyissa meg a portot 5601 való távoli hozzáférés lehetővé tételéhez a Kibana konzol az Azure parancssori felületen:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Nyissa meg a Kibana konzolt, és válassza ki **létrehozása** egy alapértelmezett index syslog Elasticsearch korábban küldött adatok alapján történő létrehozásához. 

![Keresse meg a Kibana Syslog-események](media/elasticsearch-install/kibana-index.png)

Válassza ki **felderítési** a keresés Kibana konzolon keresse meg, és a syslog-események át.

![Keresse meg a Kibana Syslog-események](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a rugalmas készlet telepítette az Azure-ban fejlesztési. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Ubuntu virtuális gép létrehozása az Azure erőforrás-csoportban
> * Elasticsearch Logstash és Kibana telepítése a virtuális gépen
> * Elasticsearch Logstash a mintaadatok küldése 
> * Nyissa meg a portok és a Kibana konzolon adatok használata