---
title: 'Interaktív hibakeresés: VS Code & ML számítási példányok (előzetes verzió)'
titleSuffix: Azure Machine Learning
description: Állítsa be a VS Code távirányítót a kód interaktív hibakereséséhez Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jmartens
author: j-martens
ms.date: 07/09/2020
ms.openlocfilehash: 46aefa62ab00c0b3bc2b90467a9fafd61439a10e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86203447"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote-preview"></a>Interaktív hibakeresés egy Azure Machine Learning számítási példányon és a VS Code Remote (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan állíthatja be a Visual Studio Code Remote-t egy Azure Machine Learning számítási példányon, hogy interaktív módon tudjon **hibakeresést végezni a** vs Code-ból. 

+ Az [Azure Machine learning számítási példány](concept-compute-instance.md) egy teljes körűen felügyelt felhőalapú munkaállomás az adatszakértők számára, és felügyeleti és vállalati készültségi képességeket biztosít a rendszergazdák számára. 


+ [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/remote-overview) A fejlesztés lehetővé teszi, hogy egy tárolót, távoli gépet vagy a Linux (WSL) Windows alrendszerét teljes funkcionalitású fejlesztői környezetként használja. 

## <a name="prerequisite"></a>Előfeltétel  

Windows-platformokon [telepítenie kell egy OpenSSH-kompatibilis SSH-ügyfelet](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) , ha még nem létezik ilyen. 

> [!Note]
> A PuTTY nem támogatott a Windowsban, mivel az SSH-parancsnak az elérési úton kell lennie. 

## <a name="get-ip-and-ssh-port"></a>IP-cím és SSH-port beszerzése 

1. Nyissa meg a Azure Machine Learning studiót a következő címen: https://ml.azure.com/ .

2. Válassza ki a [munkaterületet](concept-workspace.md).
1. Kattintson a **számítási példányok** fülre.
1. Az **alkalmazás URI** -oszlopában kattintson a távoli számításként használni kívánt számítási példány **SSH** -hivatkozására. 
1. A párbeszédpanelen jegyezze fel az IP-címet és az SSH-portot. 
1. Mentse a titkos kulcsot a ~/.ssh/könyvtárba a helyi számítógépen; például nyisson meg egy szerkesztőt egy új fájlhoz, és illessze be a kulcsot a következőbe: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   A titkos kulcs valahogy így fog kinézni:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Módosítsa a fájl engedélyeit, és győződjön meg arról, hogy csak a fájlt tudja olvasni.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Példány hozzáadása gazdagépként 

Nyissa meg a fájlt `~/.ssh/config` (Linux) vagy `C:\Users<username>.ssh\config` (Windows) egy szerkesztőben, és adjon hozzá egy ehhez hasonló új bejegyzést:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

További részletek a mezőkről: 

|Mező|Leírás|
|----|---------|
|Gazda|Bármilyen gyorsírást használhat a számítási példányhoz |
|HostName|Ez a számítási példány IP-címe. |
|Port|Ez a port a fenti SSH-párbeszédablakban látható. |
|Felhasználó|Ennek a következőnek kell lennie `azureuser` |
|IdentityFile|Arra a fájlra mutasson, ahová a titkos kulcsot mentette |

Most a fentiekben használt gyorsírással SSH-t kell tudnia használni a számítási példányhoz `ssh azmlci1` . 

## <a name="connect-vs-code-to-the-instance"></a>A VS Code összekötése a példánnyal 

1. [Telepítse a Visual Studio Code](https://code.visualstudio.com/)-ot.

1. [Telepítse a távoli SSH-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Az SSH-konfigurációk megjelenítéséhez kattintson a bal oldali távoli SSH ikonra.

1. Kattintson a jobb gombbal az imént létrehozott SSH-gazda konfigurációra.

1. Válassza a **Kapcsolódás a gazdagéphez lehetőséget az aktuális ablakban**. 

Innentől kezdve teljes mértékben dolgozik a számítási példányon, és mostantól szerkesztheti, hibakeresést végezhet, használhatja a git-t, a bővítmények használatát stb. – akárcsak a helyi Visual Studio Code-ban. 

## <a name="next-steps"></a>További lépések

Most, hogy beállította a Visual Studio Code Remote-t, a Visual Studio Code-ból távoli számításként használhat számítási példányt a kód interaktív hibakereséséhez. 

[Oktatóanyag: az első ml-modell betanítása](tutorial-1st-experiment-sdk-train.md) azt mutatja be, hogyan használható a számítási példány egy integrált jegyzetfüzettel.