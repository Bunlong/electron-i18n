# ang powerMonitor

> I-monitor ang mga pagbabago sa estado ng power.

Proseso:[Main](../glossary.md#main-process)

Hindi mo kailangan o gamitin ang amg modyul na ito hanggang ang event ng `ready` ng modyul ng `app` ay lumabas.

Halimbawa:

```javascript
const electron = kailangan('electron')
const {app} = electron

app.on('ready', () => {
  electron.powerMonitor.on('suspend', () => {
    console.log('The system is going to sleep')
  })
})
```

## Pangyayari

Ang modyul ng `powerMonitor` ay maglalabas ng mga sumusunod na event:

### Event: 'isuspindi'

Ay lalabas kapag ang sistema ay sususpindihin.

### Event: 'magpatuloy'

Ay lalabas kapag ang sistema ay nagpapatuloy.

### Event: 'on-ac' sa *Windows*

Ay lalabas kapag ang sistema ay nagbago sa AC power.

### Event: 'on-battery' sa *Windows*

Ay lalabas kapag ang sistema ay nagbago sa power ng baterya.