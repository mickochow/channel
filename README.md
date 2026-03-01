<h1 align="center"> mickochow channel </h1>

Please enjoy the show by the following URL:

`https://raw.githubusercontent.com/mickochow/channel/main/liveXX.m3u8`

=======================

001: live001.m3u8

002: live002.m3u8

003: live003.m3u8

004: live004.m3u8

005: live005.m3u8

006: live006.m3u8

007: live007.m3u8

008: live008.m3u8

009: live009.m3u8

010: live010.m3u8

011: live011.m3u8

012: live012.m3u8

013: live013.m3u8

014: live014.m3u8

015: live015.m3u8

016: live016.m3u8

017: live017.m3u8

018: live018.m3u8

019: live019.m3u8

020: live020.m3u8

021: live021.m3u8

022: live022.m3u8

023: live023.m3u8

024: live024.m3u8

025: live025.m3u8

026: live026.m3u8

027: live027.m3u8

028: live028.m3u8

029: live029.m3u8

030: live030.m3u8

031: live031.m3u8

032: live032.m3u8

033: live033.m3u8

034: live034.m3u8

035: live035.m3u8

036: live036.m3u8

037: live037.m3u8

038: live038.m3u8

039: live039.m3u8

040: live040.m3u8

041: live041.m3u8

042: live042.m3u8

043: live043.m3u8

044: live044.m3u8

045: live045.m3u8

046: live046.m3u8

047: live047.m3u8

048: live048.m3u8

049: live049.m3u8

050: live050.m3u8

051: live051.m3u8

052: live052.m3u8

053: live053.m3u8

054: live054.m3u8

055: live055.m3u8

056: live056.m3u8

057: live057.m3u8

058: live058.m3u8

059: live059.m3u8

060: live060.m3u8

061: live061.m3u8

062: live062.m3u8

063: live063.m3u8

064: live064.m3u8

065: live065.m3u8

066: live066.m3u8

067: live067.m3u8

068: live068.m3u8

069: live069.m3u8

070: live070.m3u8

071: live071.m3u8

072: live072.m3u8

073: live073.m3u8

074: live074.m3u8

075: live075.m3u8

076: live076.m3u8

077: live077.m3u8

078: live078.m3u8

079: live079.m3u8

080: live080.m3u8

081: live081.m3u8

082: live082.m3u8

083: live083.m3u8

084: live084.m3u8

085: live085.m3u8

086: live086.m3u8

087: live087.m3u8

088: live088.m3u8

089: live089.m3u8

090: live090.m3u8

091: live091.m3u8

092: live092.m3u8

093: live093.m3u8

094: live094.m3u8

095: live095.m3u8

096: live096.m3u8

097: live097.m3u8

098: live098.m3u8

099: live099.m3u8

100: live100.m3u8

101: live101.m3u8

102: live102.m3u8

103: live103.m3u8

104: live104.m3u8

105: live105.m3u8

106: live106.m3u8

107: live107.m3u8

108: live108.m3u8

109: live109.m3u8

![Media Wall](https://raw.githubusercontent.com/mickochow/cert/main/mediawall.png)

### You can create a media wall by running the following zsh script on osx
``` zsh
#!/bin/zsh

# Define the range and specific values
range=( {01..16} )
specific_values=("096" "099")

# Combine range and specific values
inputs=("${range[@]}" "${specific_values[@]}")

# Window dimensions
window_width=325
window_height=183

# Monitor centre position (example values, adjust as needed)
monitor_centre_x=860
monitor_centre_y=500

# Function to calculate window position for counterclockwise pattern without overlapping
calculate_position() {
    local index=$1
    local width=$2
    local height=$3
    local centre_x=$4
    local centre_y=$5

    # center, 1-step ring (8), 2-step ring (16) — includes diagonal corners
    local positions=(
        "0,0"
        "-$width,0"
        "-$width,-$height"
        "0,-$height"
        "$width,-$height"
        "$width,0"
        "$width,$height"
        "0,$height"
        "-$width,$height"

        "-$((2*width)),$height"
        "-$((2*width)),0"
        "-$((2*width)),-$height"
        "-$((2*width)),-$((2*height))"   # far top-left corner
        "-$width,-$((2*height))"
        "0,-$((2*height))"
        "$width,-$((2*height))"
        "$((2*width)),-$((2*height))"    # far top-right corner
        "$((2*width)),-$height"
        "$((2*width)),0"
        "$((2*width)),$height"
        "$((2*width)),$((2*height))"     # far bottom-right corner
        "$width,$((2*height))"
        "0,$((2*height))"
        "-$width,$((2*height))"
        "-$((2*width)),$((2*height))"    # far bottom-left corner
    )

    local count=${#positions[@]}

    # normalize index into 0..count-1 then convert to zsh 1-based index
    local idx=$(( index % count ))
    (( idx < 0 )) && idx=$(( idx + count ))
    idx=$(( idx + 1 ))

    local offset=${positions[idx]}

    # split without external tools
    local x_offset=${offset%%,*}
    local y_offset=${offset#*,}

    local left=$(( centre_x + x_offset ))
    local top=$(( centre_y + y_offset ))
    local right=$(( left + width ))
    local bottom=$(( top + height ))

    echo "${left},${top},${right},${bottom}"
}

osascript -e "set volume output volume 1"

# Initialize the position index
position_index=0
UA="Mozilla/5.0 (iPhone; CPU iPhone OS 18_6 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0 Mobile/15E148 Safari/604.1"

# Loop over each input and run the target script
for input in "${inputs[@]}"; do

    streamingURL="https://raw.githubusercontent.com/mickochow/channel/main/live$input.m3u8"

    # Check if the URL exists
    http_status=$(curl -o /dev/null -s -w "%{http_code}\n" --user-agent "$UA" "$streamingURL")

    if [[ "$http_status" -gt 400 ]]; then
#        echo "URL $streamingURL not found (404). Skipping to next input..."
        echo "live$input.m3u8 is not ready!"
        continue
    fi

    if [[ "$http_status" -gt 300 ]]; then
        http_status=$(curl -o /dev/null -s -w "%{http_code}\n" --user-agent "$UA" -L "$streamingURL")
#        redirect_url=$(curl -o /dev/null -s -w "%{redirect_url}\n" --user-agent "$UA" "$streamingURL")
#        echo "${input} -> ${redirect_url}[${http_status}]"
#        echo "-> ${redirect_url}[${http_status}]"

        if [[ "$http_status" -gt 400 ]]; then
            echo "live$input.m3u8 is not ready!"
            continue
        fi
    fi

    # Calculate window position for the current index
    position=$(calculate_position $position_index $window_width $window_height $monitor_centre_x $monitor_centre_y)

    # Increment the position index for the next window
    position_index=$((position_index + 1))

    # Parse position into individual coordinates
    IFS=',' read -r left top right bottom <<< "$position"

    echo "Opening live$input.m3u8 with position: [${left}, ${top}, ${right}, ${bottom}]"
    
    # Mute QuickTime Player using AppleScript
osascript <<EOF
tell application "QuickTime Player"
    activate
    open location "$streamingURL"
    delay 1
    set audio volume of document 1 to 0
    set bounds of window 1 to {$left, $top, $right, $bottom}
end tell
EOF

done

```
