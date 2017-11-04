## <a name="create-a-ruby-application"></a>Ruby-alkalmazás létrehozása
Útmutatásért lásd: [Ruby-alkalmazás létrehozása az Azure-on](../articles/virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Állítsa be az alkalmazását, használja a Service Bus számára
Service Bus használatára, töltse le, és használja az Azure Ruby csomagot, amely tartalmaz egy kényelmi szalagtár szerepel, amely a többi tárolási szolgáltatásokkal kommunikálni.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Unix).
2. Írja be a "gem telepítése azure" gem és függőségeinek telepítéséhez a parancsablakban.

### <a name="import-the-package"></a>A csomag importálása
Kedvenc szövegszerkesztőjével használ, a Ruby, amelyben a tárolás használni kívánt fájl elejéhez adja hozzá a következő:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>A Service Bus-kapcsolat beállítása
A névtér értékek, a kulcs, a kulcs, az aláíró és az állomás neve beállításához használja a következő kódot:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

A névtér értéke az a teljes URL-cím helyett létrehozott értékre állítva. Tegyük fel például, **"yourexamplenamespace"**, nem a "yourexamplenamespace.servicebus.windows.net".
