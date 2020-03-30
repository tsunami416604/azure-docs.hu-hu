---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: 16ce537a54fc77fc0f72b859d6d193501d86c1fc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67179352"
---
## <a name="create-a-ruby-application"></a>Ruby alkalmazás létrehozása
További információt a [Ruby-alkalmazás létrehozása az Azure-ban](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md)című témakörben talál.

## <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus használatára
A Service Bus használatához töltse le és használja az Azure Ruby-csomagot, amely a storage REST-szolgáltatásokkal kommunikáló kényelmi kódtárakkészletét tartalmazza.

### <a name="use-rubygems-to-obtain-the-package"></a>A csomag beszerzése a RubyGems használatával
1. Használjon egy parancssori felületet, mint például a **PowerShell** (Windows), a **Terminal** (Mac), vagy a **Bash** (Unix).
2. Írja be a "gem install azure" a parancs ablakban telepíteni a gem és a függőségek.

### <a name="import-the-package"></a>A csomag importálása
Kedvenc szövegszerkesztőjével adja hozzá a következőket annak a Ruby-fájlnak a tetejére, amelyben a tárhelyet használni kívánja:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Service Bus-kapcsolat beállítása
A következő kód dal állíthatja be a névtér, a kulcs nevét, a kulcs nevét, az aláírót és az állomást:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Állítsa be a névtér értékét a létrehozott értékre, nem pedig a teljes URL-címre. Használja például **a "yourexamplenamespace"**( a ' yourexamplenamespace.servicebus.windows.net) kifejezést, ne a "yourexamplenamespace.servicebus.windows.net".
