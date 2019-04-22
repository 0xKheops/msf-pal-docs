# REST-API for MSF PAL (2019-04-22)

# General API Information

* You need an alliance in MSF PAL, more info about MSF PAL here : [https://discord.gg/Q68yeD6](https://discord.gg/Q68yeD6)
* You need an api key which provides full access on alliance data (read or read/write depending on the type of the key)
* There is no "player-only" api keys at this time
* For most endpoints, you will need your alliance's identifier which is displayed in the admin -> alliance screen
* Api keys may be created by admins of an alliance, in admin -> API Keys screen
* Api keys may be provided in HTTP request as "api-key" header, or the ?api-key=xxx query string parameter
* All endpoints return JSON data
* Some endpoints may return CSV data if Accept=text/csv header is provided

# Read-Only Endpoints

## https://msf.pal.gg/rest/v1/characters

This endpoint returns base data for all characters (labels, traits, blitz roles).

CSV support : YES

```PowerShell
# Request data as JSON
curl https://msf.pal.gg/rest/v1/characters

# Request data as CSV
curl https://msf.pal.gg/rest/v1/characters -H "Accept: text/csv"
```

Response : 

|Property|Description|
|-|-|
|id|identifier for the character|
|avatar|identifier of the associated avatar|
|labels|labels for english and french|
|traits|array of the identifiers of the character's traits|
|blitRoles|array of identifiers of blitz roles for the character, according to Khasino's Sure2Win™ formula|

Sample JSON response : 

```json
[
    ...,
    {
        "id": "doctor-strange",
        "avatar": "doctor-strange",
        "labels": {
            "en": "Doctor Strange",
            "fr": "Docteur Strange"
        },
        "traits": [
            "hero",
            "cosmic",
            "mystic",
            "support"
        ],
        "blitzRoles": [
            "buff-purge",
            "damage-with-utility"
        ]
    },
    ...
]
```

Sample CSV response : 

```text/csv
id,avatar,traits,blitzRoles,fr,en
...
"doctor-strange","doctor-strange","hero,cosmic,mystic,support","buff-purge,damage-with-utility","Docteur Strange","Doctor Strange"
...
```

## https://msf.pal.gg/rest/v1/traits

This endpoint returns base data for all traits (translations and types).

CSV support : YES

```PowerShell
# Request data as JSON
curl https://msf.pal.gg/rest/v1/traits

# Request data as CSV
curl https://msf.pal.gg/rest/v1/traits -H "Accept: text/csv"
```

Response : 

|Property|Description|
|-|-|
|id|identifier for the trait|
|type|type of the trait|
|labels|labels for english and french|

Sample JSON response : 

```json
[
    {
        "id": "aim",
        "type": "team",
        "labels": {
            "en": "A.I.M.",
            "fr": "A.I.M."
        }
    },
    {
        "id": "asgard",
        "type": "origin",
        "labels": {
            "en": "Asgard",
            "fr": "Asgardien"
        }
    },
    ...
]
```

Sample CSV response : 

```text/csv
id,type,fr,en
"aim","team","A.I.M.","A.I.M."
"asgard","origin","Asgardien","Asgard"
...
```

## https://msf.pal.gg/rest/v1/alliance/ALLIANCE-ID

This endpoint returns all available data for a given alliance.

It is the only "read" endpoint that you should use if programming a real application (others are shortcut helpers for google sheets & excel)

CSV support : NO

```PowerShell
# With api key provided in header of the HTTP request
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518 -H "api-key: 3bc38eb4-0ecf-47e9-99b9-7c340105b2d8"

# With api key provided in the query string
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518?api-key=3bc38eb4-0ecf-47e9-99b9-7c340105b2d8

## Note that you can test these commands, it is a demo alliance
```

Response : 

|Property|Description|
|-|-|
|id|identifier for the alliance|
|name|name of the alliance|
|url|url of the alliance|
|players|array of players data (see the /players endpoint doc for details)|

Sample JSON response : 

```json
{
    "id": "5312ad0a-28a1-4681-87d4-6edd96652518",
    "name": "Alliance Demo",
    "url": "https://msf.pal.gg/demo",
    "players": [
        {
            "id": "888d1138-986f-417d-8ad4-2f44df295b03",
            "name": "Kheops",
            "avatar": "doctor-strange",
            "level": 70,
            "tcpAdmin": 1524652,
            "mtpAdmin": 145789,
            "tcp": 1916106,
            "mtp": 301973,
            "characters": [
                {
                    "id": "aim-assaulter",
                    "unlocked": true,
                    "power": 1895,
                    "level": 24,
                    "gearLevel": 4,
                    "yellowStars": 2,
                    "redStars": 3,
                    "basic": 2,
                    "special": 2,
                    "passive": 1
                },
                ...
            ],
            "teams": [
                {
                    "name": "New team",
                    "types": [
                        "raid"
                    ],
                    "restrictions": [],
                    "characters": [
                        "crossbones",
                        "daredevil",
                        "deadpool",
                        "doctor-strange",
                        "drax"
                    ],
                    "id": "1d8ea0cd-4b65-4b8d-b0e4-69db56e556fa"
                },
                ...
            ]
        },
        ...
    ]
}
```

## https://msf.pal.gg/rest/v1/alliance/ALLIANCE-ID/characters

This endpoint returns all characters of all players of the alliance

CSV support : YES

```PowerShell
# With api key provided in header of the HTTP request
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518/characters -H "api-key: 3bc38eb4-0ecf-47e9-99b9-7c340105b2d8"

# With api key provided in the query string, returning CSV
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518/characters?api-key=3bc38eb4-0ecf-47e9-99b9-7c340105b2d8 -H "Accept: text/csv"

## Note that you can test these commands, it is a demo alliance
```

Response (array of) : 

|Property|Description|
|-|-|
|id|identifier for the character|
|player|name of the player owning the character|
|unlocked|indicates whether the player owns this character|
|level|level of the character (1-70)|
|gearLevel|gear level of the character (1-13)|
|power|power of the character (1-200000)|
|yellowStars|number of yellow stars (1-7)|
|redStars|number of yellow stars (0-7)|
|basic|level of the basic capacity (1-7)|
|special|level of the special capacity (1-7)|
|ultimate|level of the ultimate capacity (1-7)|
|passive|level of the passive capacity (1-5)|

Sample JSON response : 

```json
[
    {
        "player": "Kheops",
        "id": "aim-assaulter",
        "unlocked": true,
        "level": 24,
        "power": 1895,
        "gearLevel": 4,
        "yellowStars": 2,
        "redStars": 3,
        "basic": 2,
        "special": 2,
        "passive": 1
    },
    ...
]
```

## https://msf.pal.gg/rest/v1/alliance/ALLIANCE-ID/PLAYER-ID/characters

This endpoint returns all characters of a given player

CSV support : YES
Note that you can provide a player name instead of a player id

```PowerShell
# With api key provided in header of the HTTP request
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518/888d1138-986f-417d-8ad4-2f44df295b03/characters -H "api-key: 3bc38eb4-0ecf-47e9-99b9-7c340105b2d8"

# With api key provided in header of the HTTP request, using player name
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518/Kheops/characters -H "api-key: 3bc38eb4-0ecf-47e9-99b9-7c340105b2d8"

# With api key provided in the query string, returning CSV
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518/888d1138-986f-417d-8ad4-2f44df295b03/characters?api-key=3bc38eb4-0ecf-47e9-99b9-7c340105b2d8 -H "Accept: text/csv"

## Note that you can test these commands, it is a demo alliance
```

Response (array of) : 

|Property|Description|
|-|-|
|id|identifier for the character|
|unlocked|indicates whether the player owns this character|
|level|level of the character (1-70)|
|gearLevel|gear level of the character (1-13)|
|power|power of the character (1-200000)|
|yellowStars|number of yellow stars (1-7)|
|redStars|number of yellow stars (0-7)|
|basic|level of the basic capacity (1-7)|
|special|level of the special capacity (1-7)|
|ultimate|level of the ultimate capacity (1-7)|
|passive|level of the passive capacity (1-5)|

Sample JSON response : 

```json
[
    {
        "id": "aim-assaulter",
        "unlocked": true,
        "level": 24,
        "power": 1895,
        "gearLevel": 4,
        "yellowStars": 2,
        "redStars": 3,
        "basic": 2,
        "special": 2,
        "passive": 1
    },
    ...
]
```

Sample CSV response

```text/csv
id,unlocked,level,power,gearLevel,yellowStars,redStars,basic,special,ultimate,passive
"aim-assaulter","true",24,1895,4,2,3,2,2,,1
"aim-infector","true",1,286,1,3,7,1,1,,0
...
```


## https://msf.pal.gg/rest/v1/alliance/ALLIANCE-ID/players

This endpoint returns all players of an alliance

CSV support : YES


```PowerShell
# With api key provided in header of the HTTP request
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518/players -H "api-key: 3bc38eb4-0ecf-47e9-99b9-7c340105b2d8"

# With api key provided in the query string, returning CSV
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518/players?api-key=3bc38eb4-0ecf-47e9-99b9-7c340105b2d8 -H "Accept: text/csv"

## Note that you can test these commands, it is a demo alliance
```

Response (array of) : 

|Property|Description|
|-|-|
|id|identifier for the player|
|name|name of the player|
|avatar|avatar of the player|
|level|level of the player (1-70)|
|tcpAdmin|Total collection power set by the admin|
|mtpAdmin|Max team power set by the admin|
|tcp|Total collection power calculated from the roster|
|mtp|Max team power calculated from the roster|
|characters|array of all characters|
|characters|array of all teams|

Sample JSON response : 

```json
[
    {
        "id": "888d1138-986f-417d-8ad4-2f44df295b03",
        "name": "Kheops",
        "avatar": "doctor-strange",
        "level": 70,
        "tcpAdmin": 1524652,
        "mtpAdmin": 145789,
        "tcp": 1916106,
        "mtp": 301973,
        "characters": [
            {
                "id": "aim-assaulter",
                "unlocked": true,
                "power": 1895,
                "level": 24,
                "gearLevel": 4,
                "yellowStars": 2,
                "redStars": 3,
                "basic": 2,
                "special": 2,
                "passive": 1
            },
            ...
        ],
        "teams": [
            {
                "name": "New team",
                "types": [
                    "raid"
                ],
                "restrictions": [],
                "characters": [
                    "crossbones",
                    "daredevil",
                    "deadpool",
                    "doctor-strange",
                    "drax"
                ],
                "id": "1d8ea0cd-4b65-4b8d-b0e4-69db56e556fa"
            },
            ...
        ]
    },
    ...
]
```

Sample CSV response

```text/csv
id,name,avatar,level,tcpAdmin,mtpAdmin,tcp,mtp,defense
"888d1138-986f-417d-8ad4-2f44df295b03","Kheops","doctor-strange",70,1524652,145789,1916106,301973,
"470df6c8-d2b6-4537-8013-0dd72351d37b","Player 2","ravager-stitcher",70,1234568,152635,0,0,
...
```

# Write Endpoints

## https://msf.pal.gg/rest/v1/alliance/ALLIANCE-ID/PLAYER-ID/character

This endpoint updates one character for a given player.
Body should be a JSON object with the following properties

|Property|Mandatory|Description|
|-|-|-|
|id|YES|identifier for the character|
|unlocked|YES|indicates whether the player owns this character|
|level|if unlocked|level of the character (1-70)|
|gearLevel|if unlocked|gear level of the character (1-13)|
|power|if unlocked|power of the character (1-200000)|
|yellowStars|if unlocked|number of yellow stars (1-7)|
|redStars|NO|number of yellow stars (0-7)|
|basic|NO|level of the basic capacity (1-7)|
|special|NO|level of the special capacity (1-7)|
|ultimate|NO|level of the ultimate capacity (1-7)|
|passive|NO|level of the passive capacity (1-5)|

```PowerShell
curl https://msf.pal.gg/rest/v1/alliance/5312ad0a-28a1-4681-87d4-6edd96652518/Kheops/character -H "api-key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" -d "{\"id\":\"thanos\",\"unlocked\":true,\"level\":70,\"power\":50000,\"gearLevel\":12,\"yellowStars\":7}" -H "Content-Type:application/json"
```


## https://msf.pal.gg/rest/v1/alliance/ALLIANCE-ID/PLAYER-ID/characters

This endpoint updates ALL characters for a given player. 

Player's roster will be fully overriden with the post data.

If a character isn't in the array, it will be considered as not unlocked.

Body should be a JSON array of characters (see previous endpoint for details)
