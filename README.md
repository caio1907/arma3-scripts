
# Missões - Cthulhu Chronicles

## Temporada 1

1. Chegar na casa 39  
2. Ataque ao acampamento 3C  
3. Resgate de Harjula (um Icebreaker)  
4. Ataque à estação de rádio (com Icebreaker); eliminação do rádio de longa distância  
5. Ataque ao HQ insurgente (pode chamar ataque por drone - comunicação em longa resgatada)  
6. A grande emboscada (podem chamar ataque aéreo)  

## Temporada 2

1. Entrada no FOB Chernarus N com carregamento e soldados; resgate de um blindado  
2. Patrulha no lago; visita à aldeia dos hippies (um hippie vai correr quando ver eles!)  
3. Ataque ao castelo do demônio e coleta de intel  
4. Emboscada no porto de Berezino  

## Inserções T2

1. Entrada na ilha com equipamentos pesados / madeireira  
2. Emboscada e roubo de carregamento  
3. [Ponto em branco]

---

# Configurações de Simulação Dinâmica

- **Personagens**: 1500m (limpo), 1000m (nublado)  
- **Veículos Tripulados**: 500m  
- **Objetos (Props)**: 50m  
- **Veículos Vazios**: 250m  
- **Se estiver em movimento**: distância x2  
- **Limite de distância de visão**: Ativado  

---

# Gatilhos (Triggers)

## Condições Comuns

- Sair do carro: `call{(units group "unitname") allowGetIn false;}`  
- Entrar no carro: `call{(units group "unitname") allowGetIn false;}`  
- Por hora (sem ultrapassar o dia): `daytime >= 8`  
- Dentro: `("unitname" in thisList);`  
- Fora: `!("unitname" in thisList);`  
- Ativar algo: `triggerActivated "variablename";`  
- Vários itens (AND): `&&`  
- Um ou outro (OR): `||`  
- Itens específicos nas roupas/mochilas (ACE):  
```sqf
"SatchelCharge_Remote_Mag" in (VestItems player + Uniformitems player + BackpackItems player) &&
"APERSMine_Range_Mag" in (VestItems player + Uniformitems player + BackpackItems player)
```

## Texto por Trigger

```sqf
// Condition
call{this}

// Activation
call{["Texto exibido"] spawn BIS_fnc_infoText;}
```

## Deletar Trigger

```sqf
deleteVehicle nomedotrigger;
```

## Trigger de Rádio Ativado por Outro

1. Criar trigger rádio com NONE e nomear  
2. Criar trigger ativador com:  
   `nomedotrigger setTriggerActivation ["ALPHA", "PRESENT", true];`  
3. No trigger de rádio, destruir após uso:  
   `deleteVehicle nomedotriggerdoradio;`  

---

# Visibilidade Reduzida (Ex: Nevoeiro, Neve, Mata)

```sqf
setViewDistance 200; // No marcador de lógica do lado (BLUFOR, etc.)
```

---

# Spawn Aleatório em Edifício

```sqf
if (isServer) then {
    _units = units group this;
    _house = nearestBuilding this;
    _house = _house buildingPos -1;
    _house = _house call BIS_fnc_arrayShuffle;
    if (count _units > count _house) then {_units resize (count _house);};
    {
        _x disableAI "PATH";
        _x setUnitPos selectRandom ["UP","UP","MIDDLE"];
        _x setPos (_house select _forEachIndex);
        _x addEventHandler["Fired",{
            params ["_unit"];
            _unit enableAI "PATH";
            _unit setUnitPos "AUTO";
            _unit removeEventHandler ["Fired",_thisEventHandler];
        }];
    } forEach _units;
};
```

---

## Undercover Vehicles

**Como tornar um veículo disfarçado:**
```sqf
UNITNAME addEventHandler ["getIn", {(_this select 2) setCaptive true; systemChat "You are now safe.";}];
UNITNAME addEventHandler ["getOut", {(_this select 2) setCaptive false; systemChat "You are no longer safe."}];
```

---

## INTEL - Inteligência na Missão

**Como adicionar um item de inteligência (INTEL) com imagem e texto personalizado:**
```sqf
this setVariable ["RscAttributeDiaryRecord_texture", "a3\structures_f_epc\Items\Documents\Data\document_secret_01_co.paa", true];

call {
    [INTEL2, "RscAttributeDiaryRecord", [
        "TRANSFERENCIA DE ARMARIA",
        "TRANSFERENCIA DE ARMARIA: Devido aos últimos avanços dos Guerreiros Maohi nos FOBs da OTAN achamos por bem solicitar a transferencia da nossa Armaria para algum ponto seguro da ilha. Por enquanto a armaria está escondida e protegida dentro de uma tenda no antigo forte holandês. Solicitamos que enviem um comboio para transferi-la logo após o temporal.",
        "Sgt. Murddock"
    ]] call bis_fnc_setServerVariable;
}
```

---

## Hints e Mensagens

### Hints simples:
```sqf
hint "Musica: Gimme Shelter \n Artista: Grand Funk Railroad";
```

### Hints com formatação:
```sqf
hint parseText "<t size='2.0'> Musica: Gimme Shelter \n Artista: Grand Funk Railroad </t>";
```

### Hint somente para quem está dentro do trigger:
```sqf
// Condition do trigger:
player in thisList;
```

---

## Side Chat (Mensagem lateral)

**Mensagem via SideChat:**
```sqf
Sosovu1 sideChat "Maohis de Tuvanaka, aqui é Sosovu, precisamos daquele suporte por norte!";
```

---

## Textos na Tela

### Texto simples:
```sqf
titleText ["Show this text", "PLAIN"];
titleFadeOut 20;
```

### Com tipo e tempo:
```sqf
titleText ["Show this text", "PLAIN", 2]; // 2 segundos
```

### Tipos disponíveis:
- PLAIN
- PLAIN DOWN
- BLACK
- BLACK FADED
- BLACK OUT
- BLACK IN
- WHITE OUT
- WHITE IN

### Intro escura com fade para claro:
```sqf
titleText ["<t color='#A0A0A0' size='3'> </t><br/>", "BLACK IN", -1, true, true];
titleFadeOut 20;
```

### Texto com BIS_fnc_typeText:
```sqf
any = [
    [
        ["Mission name", "<t align = 'center' size = '0.7'>%1</t><br/>"],
        ["Author name", "<t align = 'center' size = '0.7'>%1</t><br/>"],
        ["Date", "<t align = 'center' size = '0.7'>%1</t>"]
    ]
] spawn BIS_fnc_typeText;
```

### Descrição com HTML no Arma:
```html
Meu titulo<br/><br/>
um paragrafo<br/><br/>
Outro paragrafo<br/><br/>
1. uma lista <br/>
2. com varios items<br/>
3. aqui outro item<br/>
&nbsp;&nbsp;a. dessa vez com subitems<br/>
&nbsp;&nbsp;b. dessa vez com subitems<br/>
```

---

## Finalizar o Cenário

**Via Hold Action Button:**
```sqf
// Code Progress
hint "Após a conclusão do comando, a missão será finalizada.";

// Code Success
["end4", true, true] remoteExec ["BIS_fnc_endMission", 0, true];
```

---

## Slingload (Transporte Aéreo de Carga)

**No init do helicóptero:**
```sqf
_success = heli setSlingLoad nomedavariavel;
```

**O objeto (nomedavariavel) será automaticamente acoplado.**

---

## Edição e Criação de Falas (Áudio)

- Criar falas em texto para voz: [https://ttsmaker.com/br](https://ttsmaker.com/br)
- Combinar fala com efeito de estática (rádio): [https://www.veed.io](https://www.veed.io)

---
