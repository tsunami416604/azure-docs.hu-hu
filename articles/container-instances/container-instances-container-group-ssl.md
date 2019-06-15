---
title: Az Azure Container Instances szolgáltatásban az SSL engedélyezése
description: Hozzon létre egy SSL vagy TLS-végpontot egy tárolócsoport futtatása az Azure Container Instances szolgáltatásban
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 12de4ef31084d8ac8586c79ffe3d0a8e891727bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411386"
---
# <a name="enable-an-ssl-endpoint-in-a-container-group"></a>Egy tárolócsoport SSL végpont engedélyezése

Ez a cikk bemutatja, hogyan hozhat létre egy [tárolócsoport](container-instances-container-groups.md) egy alkalmazástárolót, és a egy oldalkocsi tároló egy SSL-szolgáltató. Egy külön SSL-végpont egy tárolócsoportot beállításával engedélyeznie SSL-kapcsolatot az alkalmazás az alkalmazás kódjának módosítása nélkül.

A tárolócsoport két tárolót álló állíthatja be:
* Egy alkalmazástárolót, amelynek használatával a nyilvános Microsoft egyszerű webalkalmazás fut [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) kép. 
* Egy oldalkocsi a nyilvános tárolóban [Nginx](https://hub.docker.com/_/nginx) lemezképet, az SSL használatára konfigurálva. 

Ebben a példában a tárolócsoport csak azt mutatja, 443-as porton Nginx-nyilvános IP-címmel. Az Nginx útvonalak a kísérő webalkalmazással, amely belsőleg a 80-as porton figyeli a HTTPS-kéréseket. A példában a tárolóalapú alkalmazások, amelyek más porton figyeljen tesztkörnyezetéhez igazíthatja.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ez a cikk végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Ha a következőhöz szeretne használni, helyileg, 2.0.55 verzióját, vagy később ajánlott. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Állítsa be az Nginx-SSL-szolgáltatóként, SSL-tanúsítvány szükséges. Ez a cikk bemutatja, hogyan hozhat létre, és a egy önaláírt SSL-tanúsítvány beállítása. A termelési forgatókönyvekhez hitelesítésszolgáltatótól kell tanúsítványának beszerzése.

Egy önaláírt SSL-tanúsítvány létrehozásához használja a [OpenSSL](https://www.openssl.org/) eszközt az Azure Cloud Shell és számos Linux-disztribúció elérhető, vagy egy hasonló ügyféleszköz használja az operációs rendszer.

Először hozzon létre egy tanúsítványkérelmet (.csr fájlt) egy helyi munkakönyvtárba:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Az útmutatást követve adja meg az azonosító információkat. Köznapi név adja meg a tanúsítvány állomásneve. Amikor egy jelszó megadását kéri, nyomja le az Enter anélkül, kihagyhatja a jelszóval.

Futtassa a következő parancsot az önaláírt tanúsítvány (.crt fájl) létrehozása a tanúsítványkérelemben. Példa:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Meg kell jelennie a könyvtárban található fájlok három: a tanúsítvány kérése (`ssl.csr`), a titkos kulcs (`ssl.key`), és az önaláírt tanúsítvány (`ssl.crt`). Használhat `ssl.key` és `ssl.crt` a későbbi lépésekben.

## <a name="configure-nginx-to-use-ssl"></a>Nginx-et az SSL konfigurálása

### <a name="create-nginx-configuration-file"></a>Az Nginx konfigurációs fájl létrehozása

Ez a szakasz konfigurációs fájlt az Nginx-et SSL hoz létre. Első lépésként másolja ki az alábbi szöveget egy új fájlba nevű`nginx.conf`. Az Azure Cloud Shellben használhatja a Visual Studio Code létrehozni a fájlt a munkakönyvtárban:

```console
code nginx.conf
```

A `location`, meg kell adni `proxy_pass` az alkalmazás a megfelelő portot. Ebben a példában a 80-as port elkészülünk a `aci-helloworld` tároló.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1 TLSv1.1 TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Titkos kódok Base64 kódolás és a konfigurációs fájl

Base64-kódolása az Nginx konfigurációs fájlját, az SSL-tanúsítvány és az SSL-kulcsot. A következő szakaszban adja meg a kódolt tartalmát egy YAML-fájlt a tárolócsoport üzembe helyezéséhez használt.

```console
cat nginx.conf | base64 -w 0 > base64-nginx.conf
cat ssl.crt | base64 -w 0 > base64-ssl.crt
cat ssl.key | base64 -w 0 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Tároló-csoport központi telepítése

Már üzembe helyezheti a tárolócsoport megadásával a tároló konfigurációi egy [YAML-fájl](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>YAML-fájl létrehozása

Másolja a következő yaml-kódot egy új fájlt `deploy-aci.yaml`. Az Azure Cloud Shellben használhatja a Visual Studio Code létrehozni a fájlt a munkakönyvtárban:

```console
code deploy-aci.yaml
```

Adja meg a tartalmát a base64-kódolású felsoroltak közül a fájlok `secret`. Ha például `cat` az értéknek base64 kódolású fájlok tartalmának megtekintéséhez. A telepítés során ezek a fájlok kerülnek egy [titkos kötet](container-instances-volume-secret.md) tárolócsoportban. Ebben a példában a titkos kötet csatlakoztatva van a Nginx-tároló.

```YAML
api-version: 2018-10-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>A tároló-csoport központi telepítése

Hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az-group-create) parancsot:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Üzembe helyezése az a tárolócsoportot a [az tároló létrehozása](/cli/azure/container#az-container-create) parancs, a YAML-fájl átadása argumentumként.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Központi telepítés állapotának megtekintése

Az üzembe helyezés állapotának megtekintéséhez használja a következő [az container show](/cli/azure/container#az-container-show) parancsot:

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

Sikeres telepítés kimenete az alábbihoz hasonló:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   mcr.microsoft.com/azuredocs/nginx, aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-ssl-connection"></a>SSL-kapcsolat ellenőrzése

A futó alkalmazás megtekintéséhez nyissa meg az IP-címét a böngészőben. Például az ebben a példában szereplő IP-cím van `52.157.22.76`. Használjon `https://<IP-ADDRESS>` a futó alkalmazás megtekintéséhez az Nginx-kiszolgáló konfiguráció miatt. Megpróbál kapcsolódni az `http://<IP-ADDRESS>` sikertelen.

![Képernyőkép a böngészőről, ahol egy Azure-tárolópéldányban futó alkalmazás látható](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Mivel ebben a példában egy önaláírt tanúsítványt, és nem egy hitelesítésszolgáltatótól használ, a böngésző biztonsági figyelmeztetést jelenít meg HTTPS-kapcsolaton keresztül a helyhez való csatlakozáskor. Ez várt működés.
>

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta a tárolócsoport-ban futó webalkalmazás SSL-kapcsolat engedélyezése egy Nginx-tároló beállítása. Ebben a példában az alkalmazásokat, amelyek figyelik a 80-as port-astól különböző portokon tesztkörnyezetéhez igazíthatja. Az Nginx konfigurációs fájlját (HTTP) a HTTPS PROTOKOLLT használja a 80-as porton kiszolgálókapcsolatok automatikusan átirányíthatók frissítheti is.

Amíg ez a cikk az oldalkocsi az nginx-et használ, használhatja más SSL-szolgáltató például [Caddy](https://caddyserver.com/).

Az SSL engedélyezésével egy tárolócsoport egy másik módszere az, hogy az a csoport központi telepítése egy [az Azure virtual network](container-instances-vnet.md) együtt egy [Azure application gateway](../application-gateway/overview.md). Az átjáró egy SSL-végpont is regisztrálható. Megtekinthet egy mintát [központi telepítési sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet) tesztkörnyezetéhez igazíthatja a gateway SSL-lezárás engedélyezése.
