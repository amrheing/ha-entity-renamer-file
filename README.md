# HomeAssistant Entity Renamer with input file

This project provides a Python script that allows you to list and rename entities in HomeAssistant based on regular expressions. It leverages the HomeAssistant API and WebSockets to interact with the HomeAssistant server.

This is based on 
https://github.com/saladpanda/homeassistant-entity-renamer
special thanks for the start :-)

The `homeassistant-entity-renamer.py` script provides the following functionality:

- List entities: You can retrieve a list of entities in HomeAssistant and display their friendly names and entity IDs. You can optionally filter entities using a regular expression.
- Rename entities: You can rename entities by specifying a search regular expression and a replace regular expression (see pythons [re.sub()](https://docs.python.org/3/library/re.html#re.sub)). The script will display a table with the current entity IDs, new entity IDs, and friendly names. It will ask for confirmation before renaming the entities.
- Preserves the history of renamed entities since it uses the same code path for renaming as the HomeAssistant UI (which preserves history since the release 2022.4). See [this websocket callback](https://github.com/home-assistant/core/blob/2023.7.2/homeassistant/components/config/entity_registry.py#L147).

- Edit the exported csv file with attaching the new entity name to the line. This can be imported later.
- added a testmode

Tested on HomeAssistant 2024.11.3.

## Requirements

- Python 3.6 or above
- Packages listed in `requirements.txt` or can be installed by `install_requirements.sh`

## Usage

1. Install the required packages by running the following command:
   in the newer python implementations you need to install via apt, no more with pip.
   ```
   pip install -r requirements.txt
   ```
   or:
   ````
   install_requirements.sh
   ````


2. Rename `config.py.example` to `config.py` and modify the configuration variables according to your HomeAssistant setup.

3. Run the script with the desired options:

   ```
   ./homeassistant-entity-renamer.py --search <search_regex> --replace <replace_regex>
   ```

   Replace `<search_regex>` with the regular expression that matches the entities you want to rename. Replace `<replace_regex>` with the regular expression used to rename the entities. Note that you can use all the regex magic that Python's `re.sub()` function allows.

4. Check the output and confirm the renaming process if desired.

## Usage (with docker)

1. Rename `config.py.example` to `config.py` and modify the configuration variables according to your HomeAssistant setup.
2. `docker build -t homeassistant-renamer .`
3. `docker run --rm -i homeassistant-renamer --search sensor`

## Examples

```
$ ./homeassistant-entity-renamer.py --search "interesting" --output-csv output.csv
| Friendly Name              | Current Entity ID                       | New Entity ID   |
|----------------------------|-----------------------------------------|-----------------|
| Interesting Testbutton 1   | input_button.interesting_testbutton_1   |                 |
| Interesting Testdropdown 1 | input_select.interesting_testdropdown_1 |                 |
| Interesting Testentity 1   | input_button.interesting_testentity_1   |                 |
| Interesting testnumber 1   | input_number.interesting_testnumber_1   |                 |
| interesting testtext 1     |   input_text.interesting_testtext_1     |                 |
(Table written to output.csv)
```
```
$ ./homeassistant-entity-renamer.py --search "interesting_test(.*)_1" --replace "just_another_\1"
| Friendly Name              | Current Entity ID                       | New Entity ID                      |
|----------------------------|-----------------------------------------|------------------------------------|
| Interesting Testbutton 1   | input_button.interesting_testbutton_1   | input_button.just_another_button   |
| Interesting Testdropdown 1 | input_select.interesting_testdropdown_1 | input_select.just_another_dropdown |
| Interesting Testentity 1   | input_button.interesting_testentity_1   | input_button.just_another_entity   |
| Interesting testnumber 1   | input_number.interesting_testnumber_1   | input_number.just_another_number   |
| interesting testtext 1     |   input_text.interesting_testtext_1     |   input_text.just_another_text     |

Do you want to proceed with renaming the entities? (y/N): 
Renaming process aborted.
```
```
$ ./homeassistant-entity-renamer.py --search "interesting_test(.*)_1" --replace "just_another_\1"
| Friendly Name              | Current Entity ID                       | New Entity ID                      |
|----------------------------|-----------------------------------------|------------------------------------|
| Interesting Testbutton 1   | input_button.interesting_testbutton_1   | input_button.just_another_button   |
| Interesting Testdropdown 1 | input_select.interesting_testdropdown_1 | input_select.just_another_dropdown |
| Interesting Testentity 1   | input_button.interesting_testentity_1   | input_button.just_another_entity   |
| Interesting testnumber 1   | input_number.interesting_testnumber_1   | input_number.just_another_number   |
| interesting testtext 1     |   input_text.interesting_testtext_1     |   input_text.just_another_text     |

Do you want to proceed with renaming the entities? (y/N): y
Entity 'input_button.interesting_testbutton_1' renamed to 'input_button.just_another_button' successfully!
Entity 'input_select.interesting_testdropdown_1' renamed to 'input_select.just_another_dropdown' successfully!
Entity 'input_button.interesting_testentity_1' renamed to 'input_button.just_another_entity' successfully!
Entity 'input_number.interesting_testnumber_1' renamed to 'input_number.just_another_number' successfully!
Entity 'input_text.interesting_testtext_1' renamed to 'input_text.just_another_text' successfully!

```
===== Extension for file upload ======

Create an output.csv for the entites to rename

```
$ ./homeassistant-entity-renamer.py --search "smoke" --output-csv output.csv

Friendly Name,Current Entity ID,New Entity ID
Nuki Haustüre,sensor.nuki_hausture_battery_level,
Nuki Haustüre Button Enabled,switch.nuki_hausture_button_enabled,
Nuki Haustüre Last Log,sensor.nuki_hausture_last_log,
Nuki Haustüre authorization: Ellen,switch.nuki_hausture_authorization_ellen,
Nuki Haustüre authorization: Gerald,switch.nuki_hausture_authorization_gerald_3,
Nuki Haustüre authorization: Gerald ,switch.nuki_hausture_authorization_gerald_2,
Nuki Haustüre authorization: Lydi,switch.nuki_hausture_authorization_lydi_2,
Nuki Haustüre authorization: Maike,switch.nuki_hausture_authorization_maike_2,
Nuki Haustüre authorization: Maike,switch.nuki_hausture_authorization_maike,
Nuki Haustüre authorization: Nuki Fob,switch.nuki_hausture_authorization_nuki_fob,
Nuki Haustüre authorization: iphone-ga,switch.nuki_hausture_authorization_iphone_ga,
Schalter Haustüre Action,sensor.schalter_hausture_action,
Schalter Haustüre Batterie,sensor.schalter_hausture_battery,
Schalter Haustüre Power outage count,sensor.schalter_hausture_power_outage_count,
Schalter Haustüre Temperatur,sensor.schalter_hausture_device_temperature,

```

Now edit the file, for sure, that changes wold be done easier with a search/replace regex, but it is for demonstation

```
Friendly Name,Current Entity ID,New Entity ID
Nuki Haustüre,sensor.nuki_hausture_battery_level,sensor.nuki_haustuere_battery_level,
Nuki Haustüre Button Enabled,switch.nuki_hausture_button_enabled,switch.nuki_haustuere_button_enabled
Nuki Haustüre Last Log,sensor.nuki_hausture_last_log,sensor.nuki_haustuere_last_log,
Nuki Haustüre authorization: Ellen,switch.nuki_hausture_authorization_ellen,switch.nuki_haustuere_authorization_ellen
Nuki Haustüre authorization: Gerald,switch.nuki_hausture_authorization_gerald_3,switch.nuki_haustuere_authorization_gerald,
Nuki Haustüre authorization: Gerald ,switch.nuki_hausture_authorization_gerald_2,switch.nuki_haustuere_authorization_gerald,
Nuki Haustüre authorization: Lydi,switch.nuki_hausture_authorization_lydi_2,switch.nuki_haustuere_authorization_lydi
Nuki Haustüre authorization: Maike,switch.nuki_hausture_authorization_maike_2,switch.nuki_haustuere_authorization_maike
Nuki Haustüre authorization: Maike,switch.nuki_hausture_authorization_maike,switch.nuki_haustuere_authorization_maike,
Nuki Haustüre authorization: Nuki Fob,switch.nuki_hausture_authorization_nuki_fob,switch.nuki_haustuere_authorization_nuki_fob,
Nuki Haustüre authorization: iphone-ga,switch.nuki_hausture_authorization_iphone_ga,
Schalter Haustüre Action,sensor.schalter_hausture_action,sensor.schalter_haustuere_action,
Schalter Haustüre Batterie,sensor.schalter_hausture_battery,sensor.schalter_haustuere_battery,
Schalter Haustüre Power outage count,sensor.schalter_hausture_power_outage_count,sensor.schalter_haustuere_power_outage_count,
Schalter Haustüre Temperatur,sensor.schalter_hausture_device_temperature,sensor.schalter_haustuere_device_temperature,
```

and import it to the system. First in the testmode

-t or --test
-c or --count 2 than only a few lines will be processed

```
./ha-entity-renamer-file.py -t -i tuere_new.csv -c 3
Namespace(search_regex=None, replace_regex=None, output_csv=None, input='tuere_new.csv', test=True, count='3')
No entities found for: Current Entity ID
Testmode: {"id": 1, "type": "config/entity_registry/update", "entity_id": "sensor.nuki_hausture_battery_level", "new_entity_id": "sensor.nuki_haustuere_battery_level"}
Testmode: {"id": 1, "type": "config/entity_registry/update", "entity_id": "switch.nuki_hausture_button_enabled", "new_entity_id": "switch.nuki_haustuere_button_enabled"}
```

Check if all is correct and then lets run

```
./ha-entity-renamer-file.py -i tuere_new.csv
```

You will get an output which tell's about the success


## Acknowledgements

This project was developed in cooperation with ChatGPT, a large language model trained by OpenAI, based on the GPT-3.5 architecture.

Feel free to explore and modify the script to suit your specific needs. If you encounter any issues or have suggestions for improvements, please submit them to the project's issue tracker.