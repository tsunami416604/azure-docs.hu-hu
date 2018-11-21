---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270354"
---
## <a name="create-a-ruby-application"></a>Ruby alkalmazás létrehozása
Útmutatásért lásd: [egy Ruby-alkalmazás létrehozása az Azure-ban](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Használja a Service Bus-alkalmazás konfigurálása
A Service Bus, töltse le és használja az Azure Ruby-csomagot, amely tartalmazza a kényelem kódtárak, amely kommunikálni a tárolási REST szolgáltatások.

### <a name="use-rubygems-to-obtain-the-package"></a>A csomag beszerzése a RubyGems használatával
1. Használjon egy parancssori felületet, mint például a **PowerShell** (Windows), a **Terminal** (Mac), vagy a **Bash** (Unix).
2. Írja be a "gem-telepítés azure" a parancssori ablakban, a gemet és a függőségek telepítéséhez.

### <a name="import-the-package"></a>A csomag importálása
Használja kedvenc szövegszerkesztőjét, adja hozzá a következő, amelyben a tároló használni kívánja a Ruby fájl elejéhez:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>A Service Bus-kapcsolat beállítása
A következő kód használatával állítsa be a névtér az értékeket, a kulcs, a kulcs, az aláíró és az állomás neve:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Állítsa be a névtér értéke, a létrehozott értéket adja meg a teljes URL-cím helyett. Például **"yourexamplenamespace"**, nem "yourexamplenamespace.servicebus.windows.net".
