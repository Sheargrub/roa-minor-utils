# SSL Character Select HUD Toggle  

This utility extends Lognes's SSL HUD by adding a button to the CSS to toggle the HUD type on SSL characters, making it easier to access without use of the external buddy. The script relies on get/set_synced_var(), so you may need to make adjustments if you already use those functions.  

## Installation  
If you don't already have it, you'll need to have installed the SSL HUD to your character. Please be sure to credit Lognes!  
[SSL HUD buddy (contains installation instructions)](https://steamcommunity.com/sharedfiles/filedetails/?id=2622215994)  
[SSL HUD files (mirror)](https://cdn.discordapp.com/attachments/511217874967461908/1248852305768611890/SSL-HUD-mirror.zip?ex=66652bd8&is=6663da58&hm=2f14a2bcc4b7a4203b6d49be3654674e24a83fb48cd77772dfba46d758668cf0&)  

Then, copy the contents of the above [sprites folder](https://github.com/Sheargrub/roa-minor-utils/tree/main/ssl-hud-csstoggle/sprites) to your character and add the following code anywhere within the appropriate scripts.  

### init.gml  
```gml
SSL_HUD = get_synced_var(player); // this should replace any existing code that sets SSL_HUD
```

### css_init.gml  
(You may have to create this file.)  
```gml
// Detects a fresh load. Defaulting to 2 enables the hud by default.
if (get_synced_var(player) != clamp(get_synced_var(player), 1, 2)) set_synced_var(player, 2); 

ht_status = get_synced_var(player); // should be 1 or 2, regardless of how synced_var is handled

ht_w = 30;
ht_h = 26;

ht_x_default = 74;
ht_y_default = 178;
ht_x_cpu = 60;
ht_y_cpu = 178;
ht_x_online = 172;
ht_y_online = 36;

ht_x = ht_x_default;
ht_y = ht_y_default;

ht_col_online = get_player_color(0);
ht_hovered = false;
```

### css_update.gml  
(You may have to create this file.)  
```gml
var cursor_x = get_instance_x(cursor_id);
var cursor_y = get_instance_y(cursor_id);

// Account for CPUs/online
ht_col_online = get_player_color(0);
if (get_player_hud_color(player) == c_gray) {
	ht_x = ht_x_cpu;
	ht_y = ht_y_cpu;
}
else if (get_player_hud_color(player) == ht_col_online) {
	ht_x = ht_x_online;
	ht_y = ht_y_online;
} else {
    ht_x = ht_x_default;
    ht_y = ht_y_default;
}
	
ht_hovered = (x+ht_x < cursor_x && cursor_x < x+ht_x+ht_w && y+ht_y < cursor_y && cursor_y < y+ht_y+ht_h);
suppress_cursor = ht_hovered;

// 2 enables HUD, 1 disables it
if (ht_hovered && menu_a_pressed) {
	
	ht_status = (ht_status == 2) ? 1 : 2;
    set_synced_var(player, ht_status);
    sound_play(asset_get("mfx_option"));
    
}
```

### css_draw.gml  
```gml
var ht_iindex_offset = ht_hovered ? (menu_a_down ? 4 : 2) : 0;
var col = (ht_status == 2) ? c_white : get_player_hud_color(player);
draw_sprite_ext(sprite_get("_SSL_hud_cssbutton"), ht_status-1+ht_iindex_offset, x+ht_x, y+ht_y, 1, 1, 0, col, 1);
draw_sprite(sprite_get("_SSL_hud_csspercent"), ht_status-1, x+ht_x, y+ht_y);
```

With that, the button should be added! If you'd like to change its position, you can do so by modifying the various ``ht_x`` and ``ht_y`` values in css_init. You may also want to add [Supersonic's CPU toggle helper](https://github.com/SupersonicNK/roa-workshop-templates/tree/master/css-cpu-hover) to make these sorts of buttons easier to use in local play.