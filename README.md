# VIINA / Violent Incident Information from News Articles 
## 2022 Russian Invasion of Ukraine

![All events](Figures/Maps/map_anim_latest.gif) (events)

![Control](Figures/Maps/ctr_anim_latest.gif) (territorial control)

VIINA/ВІЙНА/ВОЙНА/WAR is a near-real time multi-source event data system for the 2022 Russian Invasion of Ukraine. These data are based on news reports from Ukrainian and Russian media, which were geocoded and classified into standard conflict event categories through machine learning. 

These data are GIS-ready, with temporal precision down to the minute. Each observation is accompanied by full source information, text and URLs.

In addition to raw events, VIINA also includes data on territorial control, at the level of individual populated places.

VIINA will be updated regularly, and is freely available for use by students, journalists, policymakers, and everyday researchers. 

The most recent versions these data are available as a comma-delimited-text (csv) files within the following compressed ZIP archives:

- [Data/event_info_latest.zip](https://github.com/zhukovyuri/VIINA/tree/master/Data/event_info_latest.zip) | Raw event reports (locations, dates, urls, headlines)
- [Data/event_labels_latest.zip](https://github.com/zhukovyuri/VIINA/tree/master/Data/event_labels_latest.zip) | Event reports labeled by actor and tactic (from LSTM model)
- [Data/event_1pd_latest.zip](https://github.com/zhukovyuri/VIINA/tree/master/Data/event_1pd_latest.zip) | De-duplicated event reports and labels ("one-per-day" filter)
- [Data/control_latest.zip](https://github.com/zhukovyuri/VIINA/tree/master/Data/control_latest.zip) | Territorial control status

Note that each event data release includes both raw event reports (event_info, event_labels) and a simplified, de-duplicated data file (event_1pd). The latter uses a "one-per-day" filter to remove potential duplicate event reports, by treating multiple event reports of the same type (i.e. same combination of actor and tactic labels) in the same populated place on the same day as a single unique event.

Previous data versions are available by request (email me).

Also included are tessellated geometries of Ukrainian populated places (N = 33,141), which were used to create some of the maps on this site. These can be matched to the territorial control data by the variable `geonameid`:

- [Data/gn_UA_tess.geojson](https://github.com/zhukovyuri/VIINA/tree/master/Data/gn_UA_tess.geojson)

Please cite these data as:

 - Zhukov, Yuri (2022). "VIINA: Violent Incident Information from News Articles on the 2022 Russian Invasion of Ukraine." Ann Arbor: University of Michigan, Center for Political Studies. ([https://github.com/zhukovyuri/VIINA](github.com/zhukovyuri/VIINA), accessed [DATE]).

Corrections, feedback welcome: 

Yuri M. Zhukov. Associate Professor of Political Science, University of Michigan. 
Research Associate Professor, Center for Political Studies, Institute for Social Research.
zhukov-at-umich-dot-edu. [sites.lsa.umich.edu/zhukov](http://sites.lsa.umich.edu/zhukov).


## Data Sources

**VIINA** draws on news reports from the following Ukrainian and Russian news providers:

- [24 Канал](https://24tv.ua/) ("24tvua"): Ukrainian 24 hour news network
- [Forbes Ukraine](https://forbes.ua/) ("forbesua"): Ukrainian edition of Forbes magazine
- [Інтерфакс-Україна](https://interfax.com.ua/) ("interfaxua"): Ukrainian affiliate of Russia's Interfax news wire service
- [Комсомольская Правда](https://www.kp.ru/) ("kp"): Russian newspaper
- [ЛІГА.net](https://www.liga.net/) ("liga"): Ukrainian internet news service       
- [Live Universal Awareness Map](https://liveuamap.com/) ("liveuamap"): Ukrainian conflict event mapping service       
- [Мілітарний](https://mil.in.ua/uk/) ("militarnyy"): Ukrainian defense news portal
- [Медиазона](https://zona.media/) ("mz"): Russian news portal
- [НВ](https://nv.ua/ukr?utm_content=set_lang) ("nv"): Ukrainian magazine and internet news portal
- [Независимая Газета](https://www.ng.ru/news/) ("ng"): Russian newspaper
- [НТВ](https://www.ntv.ru/novosti/) ("ntv"): Russian television news
- [Українська правда](https://www.pravda.com.ua/) ("pravdaua"): Ukrainian newspaper
- [РИА Новости](https://ria.ru/) ("ria"): Russian news wire service
- [УНІАН](https://www.unian.info/) ("unian"): Ukrainian news wire service

This set of sources may expand/change as the war unfolds -- due to interruptions to journalistic activity from military operations, cyber attacks, and state censorship, as well as the availability of new data from other information providers.

Using an automated web scraping routine (which runs every 6 hours), VIINA extracts the text of news reports published by each source and their associated metadata (publication time and date, web urls). Using natural language processing, the system extracts and geocodes location names mentioned in each news item. A recurrent neural network then classifies each event report into several pre-defined categories.


## Geocoding

Events were geo-located by place names mentioned in the text of each news report, using APIs from Yandex and OpenStreetMaps. All unique geocoded locations were manually inspected for false positive and false negative matches.

Geocoding precision ranges from street-level (GEO_PRECISION="STREET") to province-level (GEO_PRECISION="ADM1").

Below is a map of **all** geocoded event reports since the start of Russia's military operations on February 24, 2022. Underneath the map is a timeline, showing the number of event reports published per day, across all data sources.

![All events](Figures/Maps/map_all_latest.png "All event reports, by location")
![All events](Figures/Time/time_all_latest.png "All event reports, by time")

Below are a map and timeline, showing the subset of **war-related** geocoded event reports (i.e. t_mil_b == 1, see below for details).

![War events](Figures/Maps/map_war_latest.png "War event reports, by location")
![War events](Figures/Time/time_war_latest.png "War event reports, by time")


## Event classification

To generate predicted event categories, VIINA uses a recurrent neural network (RNN) model with long short-term memory (LSTM) ([Hochreiter and Schmidhuber, 1997](https://doi.org/10.1162/neco.1997.9.8.1735); [Chang and Masterson, 2020](https://doi.org/10.1017/pan.2019.46)). LSTMs are well-suited for learning problems related to sequential data, such as sequences of words of differential length, where the vocabulary is potentially large, and where the long-term context and dependencies between inputs are potentially informative for classification (i.e. where word order and context matters, and the bag-of-words assumption is problematic). 

The current version of the data uses a training set of 3500+ randomly-selected hand-coded texts. This training set will be updated/expanded periodically as more and different types of events are added to the text corpus.

Estimation was done in Python with the Keras library.

The data currently include the following event categories:

- t_mil: Event is about war/military operations
- t_nmil: Event is not about war/military operations (e.g. human interest story)
- t_loc: Event report includes reference to specific location
- t_san: Event report mentions economic sanctions imposed on Russia
- a_rus: Event initiated by Russian or Russian-aligned armed forces
- a_ukr: Event initiated by Ukrainian or Ukrainian-aligned armed forces
- a_civ: Event initiated by civilians
- a_other: Event initiated by a third party (e.g. U.S., EU, Red Cross)
- t_aad: Anti-air defense, Buk, shoulder-fired missiles (Igla, Strela, Stinger)
- t_airstrike: Air strike, strategic bombing, helicopter strike
- t_airalert: Air raid siren/alert
- t_armor: Tank battle or assault
- t_arrest: Arrest by security services or detention of prisoners of war
- t_artillery: Shelling by field artillery, howitzer, mortar, or rockets like Grad/BM-21, Uragan/BM-27, other Multiple Launch Rocket System (MRLS)
- t_control: Establishment/claim of territorial control over population center
- t_firefight: Any exchange of gunfire with handguns, semi-automatic rifles, automatic rifles, machine guns, rocket-propelled grenades (RPGs)
- t_killing: Targeted killing or assassination
- t_ied: Improvised explosive device, roadside bomb, landmine, car bomb, explosion 
- t_raid: Assault/attack by paratroopers or special forces, usually followed by a retreat
- t_occupy: Occupation of territory or building
- t_property: Destruction of property or infrastructure
- t_cyber: Cyber operations, including DDOS attacks, website defacement
- t_hospital: Attacks on hospitals and humanitarian convoys
- t_milcas: Event report mentions military casualties
- t_civcas: Event report mentions civilian casualties

This set of categories will expand in the future, as more and different types of events are added to the text corpus.

There are two versions of each variable included in the dataset:

1. Predicted probabilities (ending with "\_pred"): predicted probability that event belongs to each category, from the LSTM model
2. Binary indicators (ending with "\_b"): dummy variables, coded 1 or 0

Cutoffs for dichotomizing the predicted probabilities were selected by minimizing Type I and Type II errors against the training set. For each variable, the algorithm considers every potential cutoff ranging from 0 to 1, compares the resulting binary values to training set labels, calculates rates of false positives and false negatives, and selects the cutoff that minimizes the sum of these rates. These cutoffs are different for each variable, and are enumerated in the table below.

Below are in-sample and out-of-same prediction accuracy statistics for each variable, along with recommended cutoffs for dichotomizing each variable (cutoff_01).

|variable    | accuracy_in_samp| accuracy_out_samp| cutoff_01|
|:-----------|----------------:|-----------------:|---------:|
|t_mil       |        0.9983416|         0.8057395| 0.9997400|
|t_loc       |        0.9994472|         0.8476821| 0.2095443|
|t_san       |        0.9994472|         0.9337748| 0.7306507|
|a_rus       |        1.0000000|         0.8520972| 0.3852877|
|a_ukr       |        0.9994472|         0.9448124| 0.9999800|
|a_civ       |        0.9939193|         0.9955850| 0.9991993|
|a_other     |        0.9535655|         0.9646799| 1.0000000|
|t_aad       |        0.9900498|         0.9867550| 0.9964787|
|t_airstrike |        1.0000000|         0.9845474| 1.0000000|
|t_airalert  |        1.0000000|         0.9977925| 0.0120273|
|t_armor     |        0.9900498|         0.9889625| 0.0119602|
|t_arrest    |        1.0000000|         0.9602649| 1.0000000|
|t_artillery |        1.0000000|         0.9492274| 0.0752815|
|t_control   |        0.9994472|         0.9823400| 1.0000000|
|t_firefight |        1.0000000|         0.9867550| 0.9936799|
|t_killing   |        1.0000000|         0.9867550| 1.0000000|
|t_ied       |        1.0000000|         0.9845474| 0.9995400|
|t_raid      |        1.0000000|         0.9933775| 0.9955199|
|t_occupy    |        1.0000000|         0.9889625| 1.0000000|
|t_property  |        1.0000000|         0.9514349| 0.9987400|
|t_cyber     |        1.0000000|         0.9602649| 0.9999800|
|t_hospital  |        1.0000000|         0.9889625| 0.0156603|
|t_milcas    |        1.0000000|         0.9779249| 0.9999400|
|t_civcas    |        1.0000000|         0.9470199| 1.0000000|

This table is updated daily and is available in csv format here: 

- [auc_latest.csv](https://github.com/zhukovyuri/VIINA/tree/master/Diagnostics/auc_latest.csv)

Note that these statistics are subject to change, as new events are added to the corpus and as the training set expands.

Below are illustrative word clouds for several categories of events. The font size is proportional to word frequencies in news wire headlines predicted as being most likely to belong to each topic category (99th percentile of predicted probability). The clouds are for out-of-sample predictions on the full set of news stories in the corpus. 

### Events about war/military operations (t_mil_*)

![wc_t_mil_test](Figures/WC/wc_t_mil_test.png)

A quick guide to what some the words mean:

- "окупанти" (okupanty) means "occupiers" (in Ukrainian)
- "зсу" (zsu) is the acronym for Armed Forces of Ukraine (in Ukrainian)


### Russian-initiated events (a_rus_*)

![wc_t_mil_test](Figures/WC/wc_a_rus_test.png)

- "окупанти" (okupanty) means "occupiers" 
- "ворог" (voroh) means "enemy"
- "війска" (viyska) means "forces"


### Ukrainian-initiated events (a_ukr_*)

![wc_t_mil_test](Figures/WC/wc_a_ukr_test.png)

- "зсу" (zsu) is the acronym for Armed Forces of Ukraine (in Ukrainian)
- "всу" (vsu) is the acronym for Armed Forces of Ukraine (in Russian)
- "заявили днр" (zayavili dnr) means "DNR has claimed" (in Russian)


### Sanctions (t_san_*)

![wc_t_mil_test](Figures/WC/wc_t_san_test.png)

- "санкції" (sanktsiyi) means "sanctions"
- "сша" (ssha) means USA
- there are also terms here for sanctions related to SWIFT, Visa, MasterCard


### Anti-air defense (t_aad_*)

![wc_t_mil_test](Figures/WC/wc_t_aad_test.png)

- "збили" (zbyly) means "shot down"
- "літак" (litak) means "aircraft"


### Air strikes (t_airstrike_*)

![wc_t_mil_test](Figures/WC/wc_t_airstrike_test.png)

- "повітряна тривога" (povitryana tryvoha) means "air raid alert"


### Arrests or detentions of POWs (t_arrest_*)

![wc_t_mil_test](Figures/WC/wc_t_arrest_test.png)

- "затримали" (zatrymaly) means "arrested" or "detained"
- "взяли в полон" (vzyaly v polon) means "taken prisoner"


### Tank battles (t_armor_*)

![wc_t_mil_test](Figures/WC/wc_t_armor_test.png)

- "танки" (tanki) means "tanks"
- "окупантів" (okupantiv) means "of occupiers"


### Territorial control (t_control_*)

![wc_t_mil_test](Figures/WC/wc_t_control_test.png)

- "голова ода" (holova oda) means "head of regional administration" (such officials sometimes make announcements about territorial control)
- "місто" (misto) means "city"
- "контролем" (kontrolem) means "[under] control"


### Firefights (t_firefight_*)

![wc_t_mil_test](Figures/WC/wc_t_firefight_test.png)

- "бої" (boyi) means "fighting" (in Ukrainian)
- "бои" (boi) means "fighting" (in Russian)


### Artillery shelling and rocket strikes (t_artillery_*)

![wc_t_mil_test](Figures/WC/wc_t_artillery_test.png)

- "обстріл" (obstril) means "shelling"
- "ракети" (rakety) means "rockets"
- "заявили днр" (zayavili dnr) means "DNR has claimed" (i.e. allegations of shelling by UA forces in Donbas)


### Raid (t_raid_*)

![wc_t_mil_test](Figures/WC/wc_t_raid_test.png)

- "наступ" (nastup) means "advance/offensive"
- "діверсантів" (diversantiv) means "of saboteurs/diversionary units"
- "висадився десант" (vysadyvsya desant) means "paratroopers landed"


### Destruction of property or infrastructure (t_property_*)

![wc_t_mil_test](Figures/WC/wc_t_property_test.png)

- "будинків" (budynkiv) means "houses"
- "з під завалів" (z pid zavaliv) means "from under the rubble"


### Cyber operations (t_cyber_*)

![wc_t_mil_test](Figures/WC/wc_t_cyber_test.png)

- "хакери" (khakery) means "hackers"
- "зламали сайт" (zlamaly sayt) means "hacked the website"


### Attacks on hospitals (t_hospital_*)

![wc_t_mil_test](Figures/WC/wc_t_hospital_test.png)

- "лікарні" (likarni) means "hospitals"
- "гуманітарні коридори" (humanitarni korydory) means "humanitarian corridors"
- "працюють" (pratsyuyut') means "are working"


### Military casualties (t_milcas_*)

![wc_t_mil_test](Figures/WC/wc_t_milcas_test.png)

- "понад" (ponad) means "more than"
- "втрати" (vtraty) means "losses"
- "окупантів" (okupantiv) means "of occupiers"
- "загинули" (zahynuly) means "died"


### Civilian casualties (t_civcas_*)

![wc_t_mil_test](Figures/WC/wc_t_civcas_test.png)

- "поранені" (poraneni) means "wounded"
- "людей" (lyudey) means "people"
- "дітей" (ditey) means "children"
- "цивільних" (tsyvil'nykh) means "civilian"


## Codebook

Raw event reports ([Data/event_info_latest.zip](https://github.com/zhukovyuri/VIINA/tree/master/Data/event_info_latest.zip))
- viina_version: Date and time of VIINA data release
- event_id: Unique event ID
- event_id_1pd: De-duplicated event ID (from one-per-day filter)
- date: Date of event report (YYYYMMDD)
- time: Time of event report (HH:MM)
- geonameid: Numeric ID of populated place
- feature_code: Type of populated place (see [full list here](https://www.geonames.org/export/codes.html))
- asciiname: Name of populated place, ASCII values
- ADM1_NAME: Name of first-level administrative unit (oblast')
- ADM1_CODE: Numerical code of first-level administrative unit (oblast')
- ADM2_NAME: Name of second-level administrative unit (rayon)
- ADM2_CODE: Numerical code of second-level administrative unit (rayon)
- longitude: Longitude coordinate of event location
- latitude: Latitude coordinate of event location
- GEO_PRECISION: geographic precision of geocoded location
- GEO_API: Geocoding API used to locate event
- location: Index of unique locations mentioned in each event
- address: Address of geocoded location
- report_id: Unique ID for report that contains the event
- source: Data source short name
- url: URL web address of event report
- text: Text of event report headline/description
- lang: Language of report (ua is Ukrainian, ru is Russian)

Event reports labeled by actor and tactic ([Data/event_labels_latest.zip](https://github.com/zhukovyuri/VIINA/tree/master/Data/event_labels_latest.zip)):
- viina_version: Date and time of VIINA data release
- event_id: Unique event ID
- event_id_1pd: De-duplicated event ID (from one-per-day filter)
- date: Date of event report (YYYYMMDD)
- time: Time of event report (HH:MM)
- geonameid: Numeric ID of populated place
- t_[event type]: Predicted probability (and binary indicator) that report describes event of each type (from LSTM model, see above)
- a_[actor]: Predicted probability (and binary indicator) that report describes event initiated by each actor (from LSTM model, see above)

De-duplicated event reports and labels ([Data/event_1pd_latest.zip](https://github.com/zhukovyuri/VIINA/tree/master/Data/event_1pd_latest.zip)):
- viina_version: Date and time of VIINA data release
- event_id_1pd: De-duplicated event ID (from one-per-day filter)
- date: Date of event report (YYYYMMDD)
- n_reports: Number of constitutive event reports 
- event_ids: IDs of constitutive events (event_id in above two files)
- sources: Sources for constitutive events (source in event_info_latest files)
- geonameid: Numeric ID of populated place
- feature_code: Type of populated place (see [full list here](https://www.geonames.org/export/codes.html))
- asciiname: Name of populated place, ASCII values
- ADM1_NAME: Name of first-level administrative unit (oblast')
- ADM1_CODE: Numerical code of first-level administrative unit (oblast')
- ADM2_NAME: Name of second-level administrative unit (rayon)
- ADM2_CODE: Numerical code of second-level administrative unit (rayon)
- longitude: Longitude coordinate of event location
- latitude: Latitude coordinate of event location
- GEO_PRECISION: geographic precision of geocoded location
- t_[event type]: Binary indicator for each event type (from LSTM model, see above)
- a_[actor]: Binary indicator for each actor (from LSTM model, see above)


## Territorial control

![All events](Figures/Maps/map_ctr_latest.png)

VIINA data on territorial control are based on three sources: VIINA event reports on territorial control, polygon map layers from DeepStateMap, and crowdsourced control maps from Wikipedia. In cases where the three data sources disagree, a "majority vote" rule is used to determine whether a populated place is under the control of Ukrainian forces, Russian forces, or is being actively contested between the two. 

The full set of Ukrainian populated places (N = 33,156) includes all locations in the [GeoNames gazetteer](https://www.geonames.org/) with feature_code's beginning in PPL\*.

Each territorial control dataset includes the following fields:

- geonameid: Numeric ID of populated place
- name: Name of populated place
- asciiname: Name of populated place, ASCII values
- alternatenames: Alternative spellings of place name
- longitude: Longitude coordinate of populated place
- latitude: Latitude coordinate of populated place          
- feature_code: Type of populated place (see [full list here](https://www.geonames.org/export/codes.html))
- ctr_[YYYYMMDDHHMMSS]: Control status, with timestamp (UA/RU/CONTESTED)

Note that the timestamp reflects the time at which the relevant data were collected (typically every six hours or so), which naturally lags behind the reality on the ground.

Territorial control data are presently missing for the following dates: 2022/03/06-2022/03/07, 2022/05/16-2022/05/18. Data for these dates will be retroactively added in future updates.

