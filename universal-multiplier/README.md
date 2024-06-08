# Universal Multiplier

Rivals of Aether doesn't have native support for fractional damage, which can be troublesome when implementing damage multipliers. The u_mult system provides a straightforward solution to this problem that scales across multiple instances of characters, increasing multiplier consistency by allowing damage to stack properly. As an added bonus, these extra bits of damage also add up when fighting alongside other existing workshop characters that rely on the same system, such as [Desperado](https://steamcommunity.com/sharedfiles/filedetails/?id=3120902361).

> Be careful: a damage multiplier alone rarely feels impactful, which can lead to a disconnect between the gamefeel of a character and the actual strength of their scaling. Always be sure to add in other tells like hitpause increases or SFX changes when applying damage or knockback buffs.

### init.gml  
```
u_mult_damage_buffer = 0; // shared variable, will be accessed by other u_mult instances
mult_add = 0.5;	          // amount to add on top of base damage
```

###other_init.gml  
```
u_mult_damage_buffer = 0;
```

###update.gml  
```
// Reset fractional damage on death
with object_index { // oPlayer or oTestPlayer
    if (!clone && (state == PS_DEAD || state == PS_RESPAWN)) {
        u_mult_damage_buffer = 0;
    }
}
```

###hit_player.gml
```
// Applies the multiplier.
var mult_damage = my_hitboxID.damage * mult_add; // extra damage due to multiplier
take_damage(hit_player_obj.player, player, floor(mult_damage));
// This segment is mostly included as an example - as long as mult_damage contains the
// fractional damage you applied, the below buffer routine will work appropriately.

// Buffer non-integer damage, apply buffer as needed
if (!hit_player_obj.clone) {
	hit_player_obj.u_mult_damage_buffer += mult_damage - floor(mult_damage);
	if (hit_player_obj.u_mult_damage_buffer >= 1) {
	    take_damage(hit_player_obj.player, player, floor(hit_player_obj.u_mult_damage_buffer));
	    hit_player_obj.u_mult_damage_buffer -= floor(hit_player_obj.u_mult_damage_buffer);
	}
}
```