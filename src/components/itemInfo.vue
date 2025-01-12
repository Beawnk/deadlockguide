<template>
    <div class="item-info" :data-item-type="item.Slot">
        <div class="header">
            <div class="left">
                <h3 class="name">{{ item.Name }}</h3>
                <h4 class="price">
                    <img :src="getImageSrc('souls_iconColored')" alt="" class="souls-icon">
                    {{ item.Cost }}
                </h4>
            </div>
            <TierItemBonus :tier="item.Tier" :type="item.Slot" />
        </div>
        <div class="content">
            <div v-for="(category, categoryName) in handleAttrs(item.Tooltip)" :key="categoryName">
                <div v-for="(item, index) in category" :key="index" :data-type="categoryName.toLowerCase()">
                    <div v-if="item.Description && category != 'Innate'" class="special-effect">
                        <p class="strip">{{ categoryName }}
                            <span v-if="item.AbilityProperties.AbilityCooldown" class="special-effect-cd">
                                {{ item.AbilityProperties.AbilityCooldown.value }}s
                            </span>
                        </p>
                        <p class="special-effect-description" v-html="item.Description"></p>
                    </div>

                    <ul v-if="Object.keys(item.AbilityProperties).length" class="attributes">
                        <li v-for="(property, propName) in item.AbilityProperties" :key="propName"
                            :data-style="property.style">
                            <div v-if="property && propName != 'AbilityCooldown'">
                                <span class="prefix">
                                    {{ getPrefixByKey(propName) }}
                                </span>
                                <span class="number">{{ property.value }}</span>
                                <span class="prop">
                                    {{ getPropByKey(propName) }}
                                </span>
                                {{ getValueByKey(propName) }}
                            </div>
                        </li>
                        <li v-for="(property, propName) in item.ElevatedAbilityProperties" :key="propName"
                            :data-style="property.style">
                            <span class="prefix">
                                {{ getPrefixByKey(propName) }}
                            </span>
                            <span class="number">{{ property.value }}</span>
                            <span class="prop">
                                {{ getPropByKey(propName) }}
                            </span>
                            {{ getValueByKey(propName) }}
                        </li>
                    </ul>

                    <div v-if="Object.keys(item.ImportantAbilityProperties).length" style="display: none;">
                        <div v-for="(property, propName) in item.ImportantAbilityProperties" :key="propName">
                            <p :data-style="property.style">
                                {{ propName }}: {{ property.value }}
                                <span v-if="property.isConditional">Conditional</span>
                            </p>
                        </div>
                    </div>

                </div>
            </div>

        </div>
    </div>
</template>

<script>
import TierItemBonus from './tierItemBonus.vue';

export default {
    props: { item: Object },
    components: { TierItemBonus },
    data() {
        return {
            propsName: {
                "AbilityCastRange": "Cast Range",
                "AbilityChannelTime": "Channel Duration",
                "AbilityDuration": "Duration",
                "AbilityLifestealPercentHero": "Spirit Lifesteal",
                "ActivatedFireRate": "Fire Rate",
                "ActiveBonusFireRate": "Fire Rate",
                "ActiveBonusLifesteal": "Bullet Lifesteal",
                "ActiveBonusMoveSpeed": "Movement Speed",
                "ActiveRadius": "Active Radius",
                "ActiveReloadPercent": "Ammo",
                "AirMoveIncreasePercent": "Air Jump/Dash Distance",
                "AmbushBonusFireRate": "Ambush Fire Rate",
                "AmbushBonusTechPower": "Ambush Spirit Power",
                "AmbushDuration": "Ambush Duration",
                "AmmoPerSoul": "Ammo Per Soul",
                "AttackDamageWhenShielded": "Weapon Damage While Shielded",
                "AuraRadius": "Radius",
                "BaseAttackDamagePercent": "Weapon Damage",
                "BaseAttackDamagePercentAtMaxDuration": "Max Weapon Damage",
                "BaseAttackDamagePercentBonus": "Weapon Damage",
                "BonusAbilityCharges": "Bonus Ability Charges",
                "BonusAbilityDurationPercent": "Ability Duration",
                "BonusAttackRangePercent": "Weapon Fall-off Range",
                "BonusBaseWeaponDamageTaken": "Weapon Damage",
                "BonusBulletSpeedPercent": "Bullet Velocity",
                "BonusClipSize": "Ammo",
                "BonusClipSizePercent": "Ammo",
                "BonusFireRate": "Fire Rate",
                "BonusFireRatePlayerUnit": "Minions Fire Rate",
                "BonusHealth": "Bonus Health",
                "BonusHealthRegen": "Health Regen",
                "BonusHeavyMeleeDamage": "Bonus Heavy Damage",
                "BonusMeleeDamagePercent": "Melee Damage",
                "BonusMoveSpeed": "Move Speed",
                "BonusPerChain": "Damage on Jump",
                "BonusSpirit": "Spirit Power",
                "BonusSpiritDuration": "Bonus Spirit Duration",
                "BonusSpiritWithMagicShield": "Spirit Power While Shielded",
                "BonusSprintSpeed": "Sprint Speed",
                "BonusZoomPercent": "Weapon Zoom",
                "BuffDuration": "Duration",
                "BuildUpPerShot": "Buildup Per Shot",
                "BulletArmorReduction": "Bullet Resist",
                "BulletDamageReflectedPct": "Bullet Damage Returned",
                "BulletLifestealPercent": "Bullet Lifesteal",
                "BulletResist": "Bullet Resist",
                "BulletResistDuration": "Stack Duration",
                "BulletResistPerStack": "Bullet Resist per Stack",
                "BulletResistReduction": "Bullet Resist Reduction",
                "BulletShieldMaxHealth": "Bullet Shield Health",
                "BulletShieldOnCast": "Bullet Shield",
                "CasterBuffDuration": "Buff Duration",
                "ChainCount": "Max Jumps",
                "ChainRadius": "Jump Radius",
                "CloseRangeBonusDamageRange": "Close Range",
                "CloseRangeBonusWeaponPower": "Weapon Damage",
                "CooldownBetweenChargeReduction": "Faster Time Between Charges",
                "CooldownReduction": "Cooldown Reduction",
                "CooldownReductionOnChargedAbilities": "Cooldown Reduction for Charged Abilities",
                "CooldownReductionWithShield": "Cooldown Reduction While Shielded",
                "CritDamagePercent": "Bonus Damage",
                "DPS": "DPS",
                "DPSMax": "Max DPS",
                "Damage": "Damage",
                "DamageDuration": "Duration",
                "DamagePerChain": "Shock Damage",
                "DamagePulseAmount": "Pulse Damage",
                "DamagePulseRadius": "Pulse Radius",
                "DamageToStack": "Damage taken to Stack",
                "DebuffDuration": "Debuff Duration",
                "DelayDuration": "Delay Duration",
                "DotDuration": "Duration",
                "DotHealthPercent": "Bleed Damage",
                "EMPDuration": "Debuff Duration",
                "EndRadius": "End Radius",
                "FervorBulletResist": "Bullet Resist",
                "FervorFireRate": "Fire Rate",
                "FervorMovespeed": "Move Speed",
                "FervorTechResist": "Spirit Resist",
                "FireRateBonus": "Fire Rate Bonus",
                "FireRateSlow": "Fire Rate Slow",
                "FireRateWhenShielded": "Fire Rate While Shielded",
                "FlyMoveSpeed": "Bonus Fly Speed",
                "FlyingBulletShield": "Bullet Shield Health",
                "FlyingTechShield": "Spirit Shield Health",
                "HeadShotBonusDamage": "Head Shot Bonus Damage",
                "HeadShotCooldown": "Cooldown",
                "HealAmpReceivePenaltyPercent": "Healing Reduction",
                "HealFromHero": "Healing From Heroes",
                "HealFromNPC": "Healing From NPCs",
                "HealOnKill": "Heal On Hero Kill",
                "HealPerStack": "Heal per Stack",
                "HealPercentAmount": "Heal Amount",
                "HealPercentPerHeadshot": "Heal Per Headshot",
                "HealthSteal": "Max HP Steal Per Bullet",
                "ImbuedBonusDuration": "Imbued Ability Duration",
                "ImbuedCooldownReduction": "Imbued Ability Cooldown Reduction",
                "ImbuedTechPower": "Imbued Ability Spirit Power",
                "ImbuedTechRangeMultiplier": "Imbued Ability Range",
                "ImpactDamage": "Damage",
                "InvisDuration": "Invisibility Duration",
                "InvisMoveSpeedMod": "Invis Sprint Speed",
                "LifestealPercentHero": "Spirit Lifesteal",
                "LocalBulletArmorReduction": "Bullet Resist",
                "LongRangeBonusWeaponPower": "Weapon Damage",
                "LongRangeBonusWeaponPowerMinRange": "Min. Distance",
                "MagicIncreasePerStack": "Spirit Amp per Stack",
                "MagicResistReduction": "Spirit Resist Reduction",
                "MaxArmorStacks": "Max Bullet Resist",
                "MaxHealth": "Max Health",
                "MaxHealthDamage": "Max Health Bonus Damage",
                "MaxHealthLossPercent": "Max Health",
                "MaxStacks": "Max Stacks",
                "MeleeDistanceScale": "Heavy Melee distance",
                "MovementSpeedBonusDuration": "Move Speed Duration",
                "MovementSpeedSlow": "Movement Slow",
                "NonImbuedBonusDuration": "Non-Imbued Ability Duration",
                "NonImbuedCooldownReduction": "Non-Imbued Ability Cooldown Reduction",
                "NonImbuedTechRangeMultiplier": "Non-Imbued Ability Range",
                "NonPlayerBonusWeaponPower": "Weapon Damage vs. NPCs",
                "NonPlayerBulletResist": "Bullet Resist vs. NPCs",
                "OutgoingDamagePenaltyPercent": "Damage Penalty",
                "ProcBonusMagicDamage": "Spirit Damage",
                "ProcChance": "Proc Chance",
                "ProcCooldown": "Max Frequency Per Target",
                "Radius": "Radius",
                "RegenDuration": "Regen Duration",
                "ReloadSpeedMultipler": "Reload Time",
                "RespawnHealthPercent": "Rebirth Health",
                "ReturnFireBulletResist": "Bullet Resist",
                "RicochetDamagePercent": "Ricochet Damage",
                "RicochetRadius": "Ricochet Range",
                "RicochetTargetsTooltipOnly": "Ricochet Targets",
                "SaviorBulletShieldHealth": "Bullet Shield",
                "SaviorMagicShieldHealth": "Spirit Shield",
                "ShieldDuration": "Shield Duration",
                "ShootDurationForMax": "Time for Max Damage",
                "ShreddersTechAmp": "Spirit Amp",
                "SlideScale": "Slide Distance",
                "SlowDuration": "Slow Duration",
                "SlowPercent": "Movement Slow",
                "SlowResistancePercent": "Movement Slow Resist",
                "SpawnTimePenalty": "Increased Respawn Time",
                "SpiritDamage": "Spirit Damage",
                "SpiritDamageReflectedPct": "Spirit Damage Returned",
                "SpiritPower": "Spirit Power",
                "SpiritPowerPerSoul": "Spirit Power Per Soul",
                "SpottedRadius": "Spot Radius",
                "Stamina": "Stamina",
                "StaminaCooldownReduction": "Stamina Recovery",
                "StatusResistancePercent": "Debuff Resist",
                "StealDuration": "Steal Duration",
                "StunDuration": "Stun Duration",
                "SummonDuration": "Summon Duration",
                "TechArmorDamageReduction": "Spirit Resist on Spirit Damage",
                "TechDamagePercent": "Damage",
                "TechPower": "Spirit Power",
                "TechRangeMultiplier": "Ability Range",
                "TechResist": "Spirit Resist",
                "TechShieldMaxHealth": "Spirit Shield Health",
                "TechShieldOnCast": "Spirit Shield",
                "TotalHealthRegen": "Total HP Regen",
                "VexBarrierBulletMaxHealth": "Bullet Shield Health",
                "VexBarrierShieldDuration": "Duration",
                "VexBarrierTechMaxHealth": "Spirit Shield Health",
                "WeaponPowerPerKill": "Weapon Damage per Kill",
                "WeaponPowerPerStack": "Weapon Damage per Stack"
            },
            propUnit: {
                "AbilityCastRange": "",
                "AbilityChannelTime": "s",
                "AbilityDuration": "s",
                "AbilityLifestealPercentHero": "%",
                "ActivatedFireRate": "%",
                "ActiveBonusFireRate": "%",
                "ActiveBonusLifesteal": "%",
                "ActiveBonusMoveSpeed": "/s",
                "ActiveRadius": "",
                "ActiveReloadPercent": "%",
                "AirMoveIncreasePercent": "%",
                "AmbushBonusFireRate": "%",
                "AmbushBonusTechPower": "",
                "AmbushDuration": "s",
                "AmmoPerSoul": "",
                "AttackDamageWhenShielded": "%",
                "AuraRadius": "",
                "BaseAttackDamagePercent": "%",
                "BaseAttackDamagePercentAtMaxDuration": "%",
                "BaseAttackDamagePercentBonus": "%",
                "BonusAbilityCharges": "",
                "BonusAbilityDurationPercent": "%",
                "BonusAttackRangePercent": "%",
                "BonusBaseWeaponDamageTaken": "%",
                "BonusBulletSpeedPercent": "%",
                "BonusClipSize": "",
                "BonusClipSizePercent": "%",
                "BonusFireRate": "%",
                "BonusFireRatePlayerUnit": "%",
                "BonusHealth": "",
                "BonusHealthRegen": "",
                "BonusHeavyMeleeDamage": "%",
                "BonusMeleeDamagePercent": "%",
                "BonusMoveSpeed": "/s",
                "BonusPerChain": "",
                "BonusSpirit": "",
                "BonusSpiritDuration": "s",
                "BonusSpiritWithMagicShield": "",
                "BonusSprintSpeed": "/s",
                "BonusZoomPercent": "%",
                "BuffDuration": "s",
                "BuildUpPerShot": "%",
                "BulletArmorReduction": "%",
                "BulletDamageReflectedPct": "%",
                "BulletLifestealPercent": "%",
                "BulletResist": "%",
                "BulletResistDuration": "s",
                "BulletResistPerStack": "%",
                "BulletResistReduction": "%",
                "BulletShieldMaxHealth": "",
                "BulletShieldOnCast": "",
                "CasterBuffDuration": "s",
                "ChainCount": "",
                "ChainRadius": "",
                "CloseRangeBonusDamageRange": "",
                "CloseRangeBonusWeaponPower": "%",
                "CooldownBetweenChargeReduction": "%",
                "CooldownReduction": "%",
                "CooldownReductionOnChargedAbilities": "%",
                "CooldownReductionWithShield": "%",
                "CritDamagePercent": "%",
                "DPS": "",
                "DPSMax": "",
                "Damage": "",
                "DamageDuration": "s",
                "DamagePerChain": "",
                "DamagePulseAmount": "",
                "DamagePulseRadius": "",
                "DamageToStack": "",
                "DebuffDuration": "s",
                "DelayDuration": "s",
                "DotDuration": "sec",
                "DotHealthPercent": "%/sec",
                "EMPDuration": "s",
                "EndRadius": "",
                "FervorBulletResist": "%",
                "FervorFireRate": "%",
                "FervorMovespeed": "/s",
                "FervorTechResist": "%",
                "FireRateBonus": "%",
                "FireRateSlow": "%",
                "FireRateWhenShielded": "%",
                "FlyMoveSpeed": "/s",
                "FlyingBulletShield": "",
                "FlyingTechShield": "",
                "HeadShotBonusDamage": "",
                "HeadShotCooldown": "s",
                "HealAmpReceivePenaltyPercent": "%",
                "HealFromHero": "",
                "HealFromNPC": "",
                "HealOnKill": "",
                "HealPerStack": "",
                "HealPercentAmount": "%",
                "HealPercentPerHeadshot": "%",
                "HealthSteal": "",
                "ImbuedBonusDuration": "%",
                "ImbuedCooldownReduction": "%",
                "ImbuedTechPower": "",
                "ImbuedTechRangeMultiplier": "%",
                "ImpactDamage": "",
                "InvisDuration": "s",
                "InvisMoveSpeedMod": "/s",
                "LifestealPercentHero": "%",
                "LocalBulletArmorReduction": "%",
                "LongRangeBonusWeaponPower": "%",
                "LongRangeBonusWeaponPowerMinRange": "",
                "MagicIncreasePerStack": "%",
                "MagicResistReduction": "%",
                "MaxArmorStacks": "%",
                "MaxHealth": "",
                "MaxHealthDamage": "%",
                "MaxHealthLossPercent": "%",
                "MaxStacks": "",
                "MeleeDistanceScale": "%",
                "MovementSpeedBonusDuration": "s",
                "MovementSpeedSlow": "%",
                "NonImbuedBonusDuration": "%",
                "NonImbuedCooldownReduction": "%",
                "NonImbuedTechRangeMultiplier": "%",
                "NonPlayerBonusWeaponPower": "%",
                "NonPlayerBulletResist": "%",
                "OutgoingDamagePenaltyPercent": "%",
                "ProcBonusMagicDamage": "",
                "ProcChance": "%",
                "ProcCooldown": "s",
                "Radius": "",
                "RegenDuration": "s",
                "ReloadSpeedMultipler": "%",
                "RespawnHealthPercent": "%",
                "ReturnFireBulletResist": "%",
                "RicochetDamagePercent": "%",
                "RicochetRadius": "",
                "RicochetTargetsTooltipOnly": "",
                "SaviorBulletShieldHealth": "",
                "SaviorMagicShieldHealth": "",
                "ShieldDuration": "s",
                "ShootDurationForMax": "s",
                "ShreddersTechAmp": "%",
                "SlideScale": "%",
                "SlowDuration": "s",
                "SlowPercent": "%",
                "SlowResistancePercent": "%",
                "SpawnTimePenalty": "s",
                "SpiritDamage": "",
                "SpiritDamageReflectedPct": "%",
                "SpiritPower": "",
                "SpiritPowerPerSoul": "",
                "SpottedRadius": "",
                "Stamina": "",
                "StaminaCooldownReduction": "%",
                "StatusResistancePercent": "%",
                "StealDuration": "s",
                "StunDuration": "s",
                "SummonDuration": "s",
                "TechArmorDamageReduction": "%",
                "TechDamagePercent": "%",
                "TechPower": "",
                "TechRangeMultiplier": "%",
                "TechResist": "%",
                "TechShieldMaxHealth": "",
                "TechShieldOnCast": "",
                "TotalHealthRegen": "",
                "VexBarrierBulletMaxHealth": "",
                "VexBarrierShieldDuration": "s",
                "VexBarrierTechMaxHealth": "",
                "WeaponPowerPerKill": "%",
                "WeaponPowerPerStack": "%"
            },
            propPrefix: {
                "AbilityCastRange": "",
                "AbilityChannelTime": "",
                "AbilityDuration": "",
                "AbilityLifestealPercentHero": "+",
                "ActivatedFireRate": "+",
                "ActiveBonusFireRate": "",
                "ActiveBonusLifesteal": "",
                "ActiveBonusMoveSpeed": "+",
                "ActiveRadius": "",
                "ActiveReloadPercent": "",
                "AirMoveIncreasePercent": "+",
                "AmbushBonusFireRate": "+",
                "AmbushBonusTechPower": "+",
                "AmbushDuration": "",
                "AmmoPerSoul": "+",
                "AttackDamageWhenShielded": "+",
                "AuraRadius": "",
                "BaseAttackDamagePercent": "+",
                "BaseAttackDamagePercentAtMaxDuration": "",
                "BaseAttackDamagePercentBonus": "+",
                "BonusAbilityCharges": "+",
                "BonusAbilityDurationPercent": "+",
                "BonusAttackRangePercent": "+",
                "BonusBaseWeaponDamageTaken": "+",
                "BonusBulletSpeedPercent": "+",
                "BonusClipSize": "+",
                "BonusClipSizePercent": "+",
                "BonusFireRate": "+",
                "BonusFireRatePlayerUnit": "+",
                "BonusHealth": "+",
                "BonusHealthRegen": "+",
                "BonusHeavyMeleeDamage": "+",
                "BonusMeleeDamagePercent": "+",
                "BonusMoveSpeed": "+",
                "BonusPerChain": "",
                "BonusSpirit": "+",
                "BonusSpiritDuration": "",
                "BonusSpiritWithMagicShield": "+",
                "BonusSprintSpeed": "+",
                "BonusZoomPercent": "+",
                "BuffDuration": "",
                "BuildUpPerShot": "",
                "BulletArmorReduction": "",
                "BulletDamageReflectedPct": "",
                "BulletLifestealPercent": "+",
                "BulletResist": "+",
                "BulletResistDuration": "",
                "BulletResistPerStack": "",
                "BulletResistReduction": "",
                "BulletShieldMaxHealth": "+",
                "BulletShieldOnCast": "",
                "CasterBuffDuration": "",
                "ChainCount": "",
                "ChainRadius": "",
                "CloseRangeBonusDamageRange": "",
                "CloseRangeBonusWeaponPower": "+",
                "CooldownBetweenChargeReduction": "+",
                "CooldownReduction": "+",
                "CooldownReductionOnChargedAbilities": "+",
                "CooldownReductionWithShield": "+",
                "CritDamagePercent": "",
                "DPS": "",
                "DPSMax": "",
                "Damage": "",
                "DamageDuration": "",
                "DamagePerChain": "",
                "DamagePulseAmount": "",
                "DamagePulseRadius": "",
                "DamageToStack": "",
                "DebuffDuration": "",
                "DelayDuration": "",
                "DotDuration": "",
                "DotHealthPercent": "",
                "EMPDuration": "",
                "EndRadius": "",
                "FervorBulletResist": "+",
                "FervorFireRate": "",
                "FervorMovespeed": "",
                "FervorTechResist": "",
                "FireRateBonus": "",
                "FireRateSlow": "",
                "FireRateWhenShielded": "+",
                "FlyMoveSpeed": "+",
                "FlyingBulletShield": "+",
                "FlyingTechShield": "+",
                "HeadShotBonusDamage": "+",
                "HeadShotCooldown": "",
                "HealAmpReceivePenaltyPercent": "",
                "HealFromHero": "",
                "HealFromNPC": "",
                "HealOnKill": "",
                "HealPerStack": "",
                "HealPercentAmount": "",
                "HealPercentPerHeadshot": "",
                "HealthSteal": "",
                "ImbuedBonusDuration": "+",
                "ImbuedCooldownReduction": "+",
                "ImbuedTechPower": "+",
                "ImbuedTechRangeMultiplier": "+",
                "ImpactDamage": "",
                "InvisDuration": "",
                "InvisMoveSpeedMod": "+",
                "LifestealPercentHero": "+",
                "LocalBulletArmorReduction": "",
                "LongRangeBonusWeaponPower": "+",
                "LongRangeBonusWeaponPowerMinRange": "",
                "MagicIncreasePerStack": "+",
                "MagicResistReduction": "",
                "MaxArmorStacks": "",
                "MaxHealth": "",
                "MaxHealthDamage": "",
                "MaxHealthLossPercent": "",
                "MaxStacks": "",
                "MeleeDistanceScale": "+",
                "MovementSpeedBonusDuration": "",
                "MovementSpeedSlow": "",
                "NonImbuedBonusDuration": "+",
                "NonImbuedCooldownReduction": "+",
                "NonImbuedTechRangeMultiplier": "+",
                "NonPlayerBonusWeaponPower": "+",
                "NonPlayerBulletResist": "+",
                "OutgoingDamagePenaltyPercent": "",
                "ProcBonusMagicDamage": "+",
                "ProcChance": "",
                "ProcCooldown": "",
                "Radius": "",
                "RegenDuration": "",
                "ReloadSpeedMultipler": "",
                "RespawnHealthPercent": "",
                "ReturnFireBulletResist": "+",
                "RicochetDamagePercent": "",
                "RicochetRadius": "",
                "RicochetTargetsTooltipOnly": "",
                "SaviorBulletShieldHealth": "",
                "SaviorMagicShieldHealth": "",
                "ShieldDuration": "",
                "ShootDurationForMax": "",
                "ShreddersTechAmp": "",
                "SlideScale": "+",
                "SlowDuration": "",
                "SlowPercent": "",
                "SlowResistancePercent": "+",
                "SpawnTimePenalty": "+",
                "SpiritDamage": "",
                "SpiritDamageReflectedPct": "",
                "SpiritPower": "+",
                "SpiritPowerPerSoul": "+",
                "SpottedRadius": "",
                "Stamina": "+",
                "StaminaCooldownReduction": "+",
                "StatusResistancePercent": "",
                "StealDuration": "",
                "StunDuration": "",
                "SummonDuration": "",
                "TechArmorDamageReduction": "",
                "TechDamagePercent": "",
                "TechPower": "+",
                "TechRangeMultiplier": "+",
                "TechResist": "+",
                "TechShieldMaxHealth": "+",
                "TechShieldOnCast": "",
                "TotalHealthRegen": "",
                "VexBarrierBulletMaxHealth": "",
                "VexBarrierShieldDuration": "",
                "VexBarrierTechMaxHealth": "",
                "WeaponPowerPerKill": "+",
                "WeaponPowerPerStack": "+"
            }
        };
    },
    mounted() {

        console.log(this.item);
        
    },
    methods: {
        getImageSrc(name) {
            return `src/assets/items/${name.toLowerCase().replace(/ /g, '_')}.png`
        },
        styleNumbers(text) {
            return text.replace(/(\d+)/g, '<span class="number">$1</span>');
        },
        handleAttrs(attributes) {
            if (attributes == undefined) return
            try {
                const jsonObject = JSON.parse(attributes);
                return jsonObject
            } catch (error) {
                console.error("Erro ao fazer parse do JSON:", error);
            }

        },
        getValueByKey(key) {
            if (!key) return
            return this.propsName[key] || '';
        },
        getPropByKey(key) {
            return this.propUnit[key] || '';
        },
        getPrefixByKey(key) {
            return this.propPrefix[key] || '';
        }
    }
}
</script>

<style lang="scss">
.item-info {
    position: absolute;
    right: 110%;
    top: 50%;
    transform: translateY(-50%);
    border-radius: 6px;
    width: 420px;
    opacity: 0;
    visibility: hidden;
    z-index: 150;
    transition: .1s;
    color: #F0E2C9;
    box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
    background-color: #C97A03;

    &[data-item-type='Armor'] {
        background-color: #7CBB1E;
    }

    &[data-item-type='Tech'] {
        background-color: #CE91FF;
    }

    .header {
        display: flex;
        justify-content: space-between;
        padding: 16px;
    }

    .left {
        text-align: left;

        .name {
            font-size: 24px;
            font-weight: bold;
            text-shadow: 1px 1px 2px #555, 0px 0px 1px #555;
        }

        .price {
            display: block;
            font-size: 18px;
            color: #98ffde;
            margin-top: 6px;
            text-shadow: 1px 1px 2px darkslategrey, 0px 0px 1px darkslategrey;
            display: flex;
            align-items: center;

            img {
                width: 12px;
                margin-right: 4px;
            }
        }
    }

    .content {
        background-color: rgba(0, 0, 0, .4);
        border-bottom-right-radius: 6px;
        border-bottom-left-radius: 6px;
        padding-bottom: 10px;

        .attributes {
            text-align: left;
            padding: 8px 16px;
            color: #fef7da;

            li {
                margin: 12px 0;
            }

            .prefix {}

            .prop {}
        }

        .special-effect {
            text-align: left;

            .strip {
                background-color: #00000080;
                padding-left: 16px;
                display: flex;
                justify-content: space-between;
                align-items: center;
                font-weight: 400;
                min-height: 28px;
            }

            .special-effect-cd {
                background-color: #0B1314;
                height: 100%;
                display: block;
                padding: 6px;
                width: 30%;
                text-align: center;
                display: block;
                font-weight: bold;
            }

            .special-effect-description {
                padding: 12px;
                font-weight: 400;
                line-height: 1.3;
            }
        }

        [data-type='passive'],
        [data-type='active'] {
            .attributes {
                background-color: rgba(0, 0, 0, 0.2);
                width: calc(100% - 32px);
                margin: 0 auto;
                border-radius: 4px;
            }
        }

    }

}
</style>