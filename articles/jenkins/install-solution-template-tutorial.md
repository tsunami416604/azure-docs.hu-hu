---
title: Oktatóanyag – Jenkins-kiszolgáló létrehozása az Azure-ban
description: Ebben az oktatóanyagban a Jenkins-t egy Azure Linux-alapú virtuális gépen telepíti a Jenkins-megoldássablonból, és létrehoz egy minta Java-alkalmazást.
keywords: jenkins, azure, devops, portál, virtuális gép, megoldássablon
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274899"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Oktatóanyag: Jenkins-kiszolgáló létrehozása Azure Linux os virtuális gépen 

Ez az oktatóanyag bemutatja, hogyan telepítheti a [Jenkinst](https://jenkins.io) egy Ubuntu Linux-alapú virtuális gépre az Azure-ral való együttműködésre konfigurált eszközökkel és beépülő modulokkal. Ha elkészült, rendelkezni fog egy Azure-ban futó Jenkins-kiszolgálóval, amely egy Java-mintaalkalmazást hoz létre a [GitHubról](https://github.com).

> [!div class="checklist"]
> * Jenkins-kiszolgáló telepítése és konfigurálása az Azure-ban
> * A Jenkins konzol elérése SSH-alagút használatával
> * Freestyle projekt létrehozása
> * A kód fordítása és a mintaalkalmazás csomagolása

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]