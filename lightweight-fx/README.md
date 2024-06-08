# Lightweight Object Particle Effects 

Spawning large numbers of hit effects can lead to significant slowdown for players with less powerful hardware. Using LWOs instead of hit fx objects can help with this issue by reducing the number of distinct objects the game has to juggle, as well as enabling unique behavior without having to make use of a with{} statement.

The below code provides a reliable framework for implementing LWO-based particles, including support for drawing in front of/behind the character and applying movement speed values. Extra behavior can easily be added by adding fields to the particle spawner function and operating on them within the update.gml code block.

> If you plan to use this code to spawn particle effects on enemies, consider modifying it to draw those particles in other_pre_draw and other_post_draw.

## init.gml
```
lfx_list = ds_list_create();
```

## unload.gml
```
ds_list_destroy(lfx_list); // important: ds_lists must be manually destroyed in this manner to prevent memory leaks
```

## Particle spawner function (usable in any update script
```
#define spawn_lfx(in_sprite, _x, _y, in_lifetime, in_spr_dir, in_foreground, in_hsp, in_vsp)
var new_lfx = {
    lfx_x : _x,
    lfx_y : _y,
    lfx_sprite_index : in_sprite,
    lfx_max_lifetime : in_lifetime,
    lfx_lifetime : 0,
    lfx_spr_dir : in_spr_dir,
    lfx_foreground : in_foreground,
    lfx_hsp : in_hsp,
    lfx_vsp : in_vsp,
};
ds_list_add(lfx_list, new_lfx);
```

## update.gml
```
for (var i = 0; i < ds_list_size(lfx_list); i++) {
    var lfx = ds_list_find_value(lfx_list, i);
    lfx.lfx_lifetime++;
    lfx.lfx_x += lfx.lfx_hsp;
    lfx.lfx_y += lfx.lfx_vsp;
    if (lfx.lfx_lifetime >= lfx.lfx_max_lifetime) {
        ds_list_remove(lfx_list, lfx);
        i--;
    }
}
```

## pre_draw.gml
```
for (var i = 0; i < ds_list_size(lfx_list); i++) {
    var lfx = ds_list_find_value(lfx_list, i);
    if (!lfx.lfx_foreground) { // only draw bg particles
    	var lfx_image_index = lfx.lfx_lifetime * (sprite_get_number(lfx.lfx_sprite_index) / lfx.lfx_max_lifetime);
    	draw_sprite_ext(lfx.lfx_sprite_index, lfx_image_index, lfx.lfx_x, lfx.lfx_y, lfx.lfx_spr_dir, 1, 0, c_white, 1 );
    }
}
```

## post_draw.gml
```
for (var i = 0; i < ds_list_size(lfx_list); i++) {
    var lfx = ds_list_find_value(lfx_list, i);
    if (lfx.lfx_foreground) {
    	var lfx_image_index = lfx.lfx_lifetime * (sprite_get_number(lfx.lfx_sprite_index) / lfx.lfx_max_lifetime);
    	draw_sprite_ext(lfx.lfx_sprite_index, lfx_image_index, lfx.lfx_x, lfx.lfx_y, lfx.lfx_spr_dir, 1, 0, c_white, 1 );
    }
}
```