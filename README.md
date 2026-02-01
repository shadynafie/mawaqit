# MAWAQIT Prayer Times for Home Assistant

### Smart home, smart mosque -- automate your life around prayer times

ٱلسَّلَامُ عَلَيْكُمْ وَرَحْمَةُ ٱللَّٰهِ وَبَرَكَاتُهُ

---

> This is a maintained fork of the original [mawaqit/home-assistant](https://github.com/mawaqit/home-assistant) integration. Full credit goes to the original MAWAQIT team ([@MAWAQIT](https://github.com/MAWAQIT), [@moha-tah](https://github.com/moha-tah), [@Yeyvo](https://github.com/Yeyvo)). This fork fixes authentication issues that prevented prayer times from loading.

---

## What is this?

This integration brings your mosque's prayer times from [mawaqit.net](https://mawaqit.net) directly into Home Assistant. You can use it to:

- Display prayer times and iqama times on your dashboard
- Play the adhan automatically on a speaker at prayer time
- Turn on lights before Fajr
- Open shutters at Shuruq
- Any other automation you can imagine!

**You need a free account on [mawaqit.net](https://mawaqit.net) to use this integration.**

---

## Installation

### Option 1: HACS (Recommended)

1. Open **HACS** in Home Assistant
2. Click the 3-dot menu (top right) and select **Custom repositories**
3. Paste this URL: `https://github.com/shadynafie/mawaqit`
4. Select **Integration** as the category
5. Click **Add**
6. Search for **"MAWAQIT"** in HACS and click **Install**
7. **Restart Home Assistant**

### Option 2: Manual

1. Download the `custom_components/mawaqit` folder from this repository
2. Copy it into your Home Assistant `custom_components` directory (create it if it doesn't exist)
3. **Restart Home Assistant**

---

## Setup

1. After restarting, go to **Settings > Devices & Services > Add Integration**
2. Search for **"Mawaqit"**
3. Enter your **mawaqit.net** username and password
4. Choose how to find your mosque:
   - **By location** -- finds mosques within 20 km of your Home Assistant coordinates
   - **By keyword** -- search for any mosque by name
5. Select your preferred mosque from the list
6. Done! Your sensors will appear automatically

### Changing your mosque later

Go to **Settings > Devices & Services**, find MAWAQIT, click **Configure**, and select a different mosque from the list.

---

## Available sensors

The integration creates the following sensors:

| Sensor | Description |
|--------|-------------|
| Fajr, Dhuhr, Asr, Maghrib, Isha | Prayer times (adhan) |
| Fajr Iqama, Dhuhr Iqama, Asr Iqama, Maghrib Iqama, Isha Iqama | Iqama times |
| Shurouq / Sunrise | Sunrise time |
| Jumua, Jumua 2 | Friday prayer times |
| Next Salat Name | Name of the next prayer |
| Next Salat Time | Time of the next prayer |
| Next Salat Preparation | Time to prepare for next prayer |
| Mosque Label, Localisation, URL, Image | Your mosque's information |

---

## Automation examples

### Play adhan at prayer time

```yaml
- id: "isha_adhan"
  alias: Play Isha Adhan
  trigger:
    - platform: template
      value_template: >
        {% set isha_time = as_timestamp(states("sensor.isha_adhan")) | timestamp_custom("%H:%M", True) %}
        {% set time = states("sensor.time") %}
        {{ time == isha_time }}
  action:
    - action: media_player.play_media
      target:
        entity_id: media_player.living_room_speaker
      data:
        media_content_id: "http://YOUR_SERVER/adhan.mp3"
        media_content_type: music
  mode: single
```

### Turn on lights 20 minutes before Fajr

```yaml
- id: "fajr_wakeup"
  alias: Turn on bedroom light before Fajr
  trigger:
    - platform: template
      value_template: >
        {% set before = (as_timestamp(states("sensor.fajr_adhan")) - 20 * 60) | timestamp_custom("%H:%M", True) %}
        {% set time = states("sensor.time") %}
        {{ time == before }}
  action:
    - action: light.turn_on
      target:
        entity_id: light.bedroom
  mode: single
```

> **Note:** Make sure you have the `time_date` sensor configured in your Home Assistant for these automations to work:
>
> ```yaml
> sensor:
>   - platform: time_date
>     display_options:
>       - "time"
> ```

---

## Troubleshooting

**Getting "authentication failed" or 401 errors?**
Delete the MAWAQIT integration from Settings > Devices & Services, restart Home Assistant, and add it again. This ensures fresh credentials are stored.

**No mosques found near you?**
Check that your Home Assistant location (Settings > System > General) has the correct latitude and longitude. Alternatively, use the **keyword search** option to find your mosque by name.

**Integration not showing up after install?**
Make sure you restarted Home Assistant after installing via HACS or manually.

---

## Credits

This integration is based on the work of the [MAWAQIT](https://mawaqit.net) team. The original repository is at [mawaqit/home-assistant](https://github.com/mawaqit/home-assistant).

This fork (v3.1.0) fixes critical authentication issues that prevented prayer times from being fetched, along with several other stability improvements.

---

## Francais

Ce composant integre les horaires de prieres de votre mosquee [mawaqit.net](https://mawaqit.net) dans Home Assistant.

### Installation via HACS

1. Ouvrez **HACS** dans Home Assistant
2. Menu 3 points > **Custom repositories**
3. Collez l'URL : `https://github.com/shadynafie/mawaqit`
4. Categorie : **Integration**
5. Cliquez **Add**, puis installez **MAWAQIT**
6. **Redemarrez Home Assistant**

### Configuration

1. Apres le redemarrage, allez dans **Parametres > Appareils et Services > Ajouter une integration**
2. Cherchez **"Mawaqit"**
3. Entrez vos identifiants **mawaqit.net**
4. Choisissez votre methode de recherche :
   - **Par coordonnees** -- trouve les mosquees dans un rayon de 20 km
   - **Par mot-cle** -- cherchez une mosquee par son nom
5. Selectionnez votre mosquee

L'integration cree des capteurs pour les 5 horaires de prieres, les iqamas, le Shuruq, les horaires de Jumu'a, ainsi qu'un capteur `sensor.my_mosque` qui resume toutes les donnees de votre mosquee.
