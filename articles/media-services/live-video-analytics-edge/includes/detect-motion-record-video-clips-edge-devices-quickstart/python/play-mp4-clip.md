---
ms.openlocfilehash: f4a1cc432a50a555fe6e050ca318b4cfaf1092d4
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682368"
---
Az MP4-fájlokat a peremhálózati eszköz egy olyan könyvtárába írja a rendszer, amelyet a *. env* fájlban konfigurált a OUTPUT_VIDEO_FOLDER_ON_DEVICE kulcs használatával. Ha az alapértelmezett értéket használta, akkor az eredményeknek a */var/Media/* mappában kell lenniük.

Az MP4-klip lejátszása:

1. Nyissa meg az erőforráscsoportot, keresse meg a virtuális gépet, majd az Azure Bastion használatával kapcsolódjon.

    ![Erőforráscsoport](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. Jelentkezzen be az [Azure-erőforrások beállításakor](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources)létrehozott hitelesítő adatok használatával. 
1. A parancssorban nyissa meg a megfelelő könyvtárat. Az alapértelmezett hely a */var/Media*. Ekkor megjelenik az MP4-fájlok a könyvtárban.

    ![Kimenet](../../../media/quickstarts/samples-output.png) 

1. A [biztonságos másolás (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) használatával másolja a fájlokat a helyi gépre. 
1. A fájlokat a [VLC Media Player](https://www.videolan.org/vlc/) vagy bármely más MP4-lejátszó használatával játssza le.
