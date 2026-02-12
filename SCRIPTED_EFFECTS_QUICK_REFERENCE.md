# Scripted Effects Quick Reference

## InitEffect on Initializers

### Star System Example
```yaml
- StarSystem:
    UniqueId: "my_system"
    InitEffect:
      - type: "scripted_effect"
        payload:
          effect_id: "generate_home_system_resources"
```

### Planet Example
```yaml
- CelestialBody:
    UniqueId: "my_planet"
    InitEffect:
      - type: "add_deposit"
        payload:
          deposit: "d_minerals_5"
      - type: "prevent_anomaly"
```

### Unit Example
```yaml
- Unit:
    UniqueId: "elite_scout"
    InitEffect:
      - type: "add_modifier"
        payload:
          modifier: "veteran_unit"
          days: -1
```

## Common Scripted Effects

### Resource Setup
```yaml
ScriptedEffects:
  - UniqueId: "grant_starting_stockpile"
    Effects:
      - type: "modify_resource"
        payload:
          resource: "minerals"
          amount: 200
      - type: "modify_resource"
        payload:
          resource: "energy"
          amount: 150
```

### System Setup
```yaml
  - UniqueId: "setup_mining_system"
    Effects:
      - type: "every_system_planet"
        payload:
          limit:
            OR:
              - is_asteroid: true
              - is_planet_class: "pc_barren"
          effects:
            - type: "add_deposit"
              payload:
                deposit: "d_minerals_3"
```

### Flag Propagation
```yaml
  - UniqueId: "mark_empire_cluster"
    Effects:
      - type: "every_neighbor_system"
        payload:
          effects:
            - type: "set_flag"
              payload:
                flag: "empire_cluster"
```

## Common Conditions

### Simple Checks
```yaml
condition:
  has_flag: "empire_home_system"
```

```yaml
condition:
  is_planet_class: "pc_desert"
```

```yaml
condition:
  colonizable_planet: true
```

### Logical Operators
```yaml
condition:
  AND:
    - has_flag: "home_system"
    - NOT:
        has_flag: "explored"
```

```yaml
condition:
  OR:
    - is_planet_class: "pc_arctic"
    - is_planet_class: "pc_tundra"
    - is_planet_class: "pc_alpine"
```

```yaml
condition:
  NOR:  # None of these can be true
    - is_asteroid: true
    - is_star: true
```

## Common Patterns

### Conditional Resource Grant
```yaml
Effects:
  - type: "if"
    payload:
      condition:
        has_flag: "rich_system"
      effects:
        - type: "every_system_planet"
          payload:
            effects:
              - type: "add_deposit"
                payload:
                  deposit: "d_bonus_minerals"
```

### Nested Iteration
```yaml
Effects:
  - type: "every_neighbor_system"
    payload:
      effects:
        - type: "every_system_planet"
          payload:
            limit:
              colonizable_planet: true
            effects:
              - type: "set_flag"
                payload:
                  flag: "survey_target"
```

### Random Selection with Fallback
```yaml
Effects:
  - type: "random_system_planet"
    payload:
      limit:
        is_planet_class: "pc_gas_giant"
      effects:
        - type: "add_deposit"
          payload:
            deposit: "d_energy_5"
  
  # Fallback if no gas giant found
  - type: "if"
    payload:
      condition:
        NOT:
          has_flag: "deposit_added"
      effects:
        - type: "system_star"
          payload:
            effects:
              - type: "add_deposit"
                payload:
                  deposit: "d_energy_3"
```

### Counted Loops
```yaml
Effects:
  - type: "while"
    payload:
      count: 3
      effects:
        - type: "random_system_planet"
          payload:
            limit:
              NOT:
                has_flag: "deposit_added"
            effects:
              - type: "add_deposit"
                payload:
                  deposit: "d_random_mineral"
              - type: "set_flag"
                payload:
                  flag: "deposit_added"
```

## Effect Types Cheat Sheet

| Effect | Scope | Description |
|--------|-------|-------------|
| `scripted_effect` | Any | Call named effect |
| `every_system_planet` | System | Iterate planets |
| `random_system_planet` | System | Random planet |
| `every_neighbor_system` | System | Iterate neighbors |
| `system_star` | System | Target primary star |
| `capital_scope` | Empire | Target capital |
| `every_playable_country` | Global | Iterate empires |
| `if` | Any | Conditional |
| `while` | Any | Loop |
| `add_deposit` | Body | Add resource |
| `clear_deposits` | Body | Remove all deposits |
| `add_modifier` | Any | Add timed modifier |
| `set_flag` | Any | Set entity/global flag |
| `add_flag` | Global | Set global flag |
| `remove_flag` | Any | Remove flag |
| `modify_resource` | Empire | Add/subtract resource |
| `set_resource` | Empire | Set exact amount |
| `prevent_anomaly` | Body | Block anomalies |
| `trigger_event` | Any | Fire event |
| `set_global_flag` | Global | Game-wide flag |

## Condition Types Cheat Sheet

| Condition | Description |
|-----------|-------------|
| `has_flag: "name"` | Entity has flag |
| `has_star_flag: "name"` | System has flag |
| `has_planet_flag: "name"` | Planet has flag |
| `is_planet_class: "pc_X"` | Planet type check |
| `is_star_class: "sc_X"` | Star type check |
| `colonizable_planet: bool` | Can colonize |
| `is_asteroid: bool` | Is asteroid |
| `is_star: bool` | Is star |
| `is_moon: bool` | Is moon |
| `has_deposit: "d_X"` | Has deposit |
| `has_modifier: "mod_X"` | Has modifier |
| `has_origin: "origin_X"` | Empire origin |
| `has_civic: "civic_X"` | Empire civic |
| `has_ethic: "ethic_X"` | Empire ethic |
| `is_gestalt: bool` | Gestalt empire |
| `is_machine_empire: bool` | Machine empire |
| `is_hive_empire: bool` | Hive empire |

## Troubleshooting

### Effect Not Executing

1. Check YAML indentation (use spaces, not tabs)
2. Verify `type:` field matches effect name exactly
3. Check console for "Unknown effect type" warnings
4. Ensure ScriptedEffectLoader is registered

### Condition Not Working

1. Verify condition syntax (proper AND/OR/NOT nesting)
2. Check entity has required components
3. Test with simple condition first
4. Check console for condition evaluation logs

### Scripted Effect Not Found

1. Verify UniqueId matches effect_id exactly
2. Check file is in `Scripts/Events/ScriptedEffects/` directory
3. Ensure file uses `ScriptedEffects:` key (capital S)
4. Restart game to reload content

### Wrong Entity Targeted

1. Check scope type (StarSystem vs CelestialBody)
2. Verify scope chain (Current vs Root vs Prev)
3. Use logging to trace scope changes
4. Test with simple effects first

## Template: Complete System with InitEffect

```yaml
# Complete example: Empire home system with scripted effects
Initializers:
  - StarSystem:
      UniqueId: "custom_empire_start"
      Usage: "empire_system_init"
      Flags: ["empire_home_system", "custom_system"]
      
      InitEffect:
        # Step 1: Mark cluster
        - type: "every_neighbor_system"
          payload:
            effects:
              - type: "set_flag"
                payload:
                  flag: "empire_cluster"
        
        # Step 2: Add system resources
        - type: "system_star"
          payload:
            effects:
              - type: "add_deposit"
                payload:
                  deposit: "d_energy_5"
        
        # Step 3: Setup planets conditionally
        - type: "every_system_planet"
          payload:
            effects:
              - type: "if"
                payload:
                  condition:
                    is_asteroid: true
                  effects:
                    - type: "add_deposit"
                      payload:
                        deposit: "d_minerals_5"
              
              - type: "if"
                payload:
                  condition:
                    AND:
                      - colonizable_planet: true
                      - NOT:
                          has_flag: "home_planet"
                  effects:
                    - type: "add_deposit"
                      payload:
                        deposit: "d_mixed_resources"
        
        # Step 4: Call custom scripted effect
        - type: "scripted_effect"
          payload:
            effect_id: "custom_system_finalization"
      
      Children:
        - UniqueId: "rl_standard_stars"
        - UniqueId: "rl_habitable_planets"
          Orbit:
            SemiMajorAxis: 1.5
          InitEffect:
            - type: "set_flag"
              payload:
                flag: "home_planet"
            - type: "scripted_effect"
              payload:
                effect_id: "generate_empire_home_planet"
```

---

For more details, see `SCRIPTED_EFFECTS_MODDING_GUIDE.md`
