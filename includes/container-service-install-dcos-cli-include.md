<properties
   pageTitle="A DC/OS parancssori felület telepítése | Microsoft Azure"
   description="A DC/OS parancssori felület telepítése."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Erre a DC/OS-alapú ACS-fürtökkel végzett munka esetén van szükség. A Swarm-alapú ACS-fürtök esetén erre a lépésre nincs szükség.

Először is, [csatlakozzon a DC/OS-alapú ACS-fürthöz](../articles/container-service/container-service-connect.md). Ezután az alábbi parancsok használatával telepítheti a DC/OS parancssori felületet az ügyfélgépre:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Ha a Python egy régebbi verzióját használja, előfordulhat, hogy „InsecurePlatformWarnings” figyelmeztetésekbe ütközik. Ezek biztonságosan figyelmen kívül hagyhatók.

A parancskörnyezet újraindítása nélküli indításhoz futtassa a következő parancsot:

```bash
source ~/.bashrc
```

Új parancskörnyezet indítása esetén ez a lépés elhagyható.

Most meggyőződhet arról, hogy a parancssori felület telepítve van:

```bash
dcos --help
```


<!--HONumber=Jun16_HO2-->


