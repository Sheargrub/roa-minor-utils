# SSL Character Select HUD Toggle  

This utility adds a button to the CSS to toggle the HUD type on SSL characters while maintaining compatibility with the original SSL HUD buddy. It relies on get/set_synced_var(), so you may need to make adjustments if you already use those functions.  

## Installation  
If you don't already have it, you'll need to have installed the SSL HUD by Lognes to your character. [TODO: add link]  

Then, copy the contents of the above sprites folder to your character and add the following code to your character anywhere within the appropriate scripts.  

### init.gml  
```
SSL_HUD = get_synced_var(player); // this should replace any existing code that sets SSL_HUD
```

### css_init.gml  
(You may have to create this file.)  
```
// Detects a fresh load. Defaulting to 2 enables the hud by default.
if (get_synced_var(player) != clamp(get_synced_var(player), 1, 2)) set_synced_var(player, 2); 

ht_status = get_synced_var(player); // should be 1 or 2, regardless of how synced_var is handled

ht_lx = 66;
ht_rx = ht_lx+30;
ht_ty = 169;
ht_by = ht_ty+26;
```

### css_draw.gml  
```
var cursor_x = get_instance_x(cursor_id);
var cursor_y = get_instance_y(cursor_id);

var ht_hovered = (temp_x+ht_lx < cursor_x && cursor_x < temp_x+ht_rx && temp_y+ht_ty < cursor_y && cursor_y < temp_y+ht_by);

// 2 enables HUD, 1 disables it
if (ht_hovered && menu_a_pressed) {
	ht_status = (ht_status == 2) ? 1 : 2;
    set_synced_var(player, ht_status);
    sound_play(asset_get("mfx_forward"));
    ht_flash_timer = 0;
    ht_iindex_offset = 4;
}

var ht_iindex_offset = ht_hovered ? (menu_a_down ? 4 : 2) : 0;
var col = (ht_status == 2) ? c_white : get_player_hud_color(player);
draw_sprite_ext(sprite_get("_SSL_hud_cssbutton"), ht_status-1+ht_iindex_offset, temp_x+ht_lx, temp_y+ht_ty, 1, 1, 0, col, 1);
draw_sprite(sprite_get("_SSL_hud_csspercent"), ht_status-1, temp_x+ht_lx, temp_y+ht_ty);
```