---
title: Oktatóanyag – Jenkins-kiszolgáló létrehozása az Azure-ban
description: Ebben az oktatóanyagban a Jenkins-t egy Azure-beli linuxos virtuális gépre telepíti a Jenkins Solution sablonból, és létrehoz egy minta Java-alkalmazást.
keywords: jenkins, azure, devops, portál, virtuális gép, megoldássablon
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274899"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Oktatóanyag: Jenkins-kiszolgáló létrehozása Azure Linux rendszerű virtuális gépen 

Ez az oktatóanyag bemutatja, hogyan telepítheti a [Jenkins](https://jenkins.io) -t egy Ubuntu Linux virtuális gépre az Azure-hoz való együttműködésre konfigurált eszközökkel és beépülő moduljaival. Ha elkészült, rendelkezni fog egy Azure-ban futó Jenkins-kiszolgálóval, amely egy Java-mintaalkalmazást hoz létre a [GitHubról](https://github.com).

> [!div class="checklist"]
> * Jenkins-kiszolgáló telepítése és konfigurálása az Azure-ban
> * A Jenkins-konzol elérése SSH-alagút használatával
> * Gyors projekt létrehozása
> * A mintakód fordítása és a minta alkalmazás csomagolása

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]