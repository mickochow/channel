<h1 align="center"> mickochow channel </h1>

Please enjoy the show by the following URL:

`https://raw.githubusercontent.com/mickochow/channel/main/liveXX.m3u8`

=======================

01: Homantin Hill

02: Yuen Long Tin Shui Wai

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
    
    local positions=(
        "0,0"                    # Center
        "-$width,0"              # Left
        "-$width,-$height"       # Top-left
        "0,-$height"             # Top
        "$width,-$height"        # Top-right
        "$width,0"               # Right
        "$width,$height"         # Bottom-right
        "0,$height"              # Bottom
        "-$width,$height"        # Bottom-left
        "-$((2 * $width)),0"     # Far left
        "-$((2 * $width)),-$height" # Far top-left
        "-$width,-$((2 * $height))" # Far top
        "0,-$((2 * $height))"    # Far top-right
        "$width,-$((2 * $height))"   # Far top-right
        "$((2 * $width)),0"      # Far right
        "$((2 * $width)),$height"   # Far bottom-right
        "$width,$((2 * $height))"   # Far bottom-right
        "0,$((2 * $height))"     # Far bottom
        "-$width,$((2 * $height))"  # Far bottom-left
    )

    local offset=${positions[$index % ${#positions[@]}]}
    
    local x_offset=$(echo $offset | cut -d',' -f1)
    local y_offset=$(echo $offset | cut -d',' -f2)
    
    local left=$((centre_x + x_offset))
    local top=$((centre_y + y_offset))
    local right=$((left + width))
    local bottom=$((top + height))
    
    echo "$left,$top,$right,$bottom"
}

# Initialize the position index
position_index=1

# Loop over each input and run the target script
for input in "${inputs[@]}"; do

    streamingURL="https://raw.githubusercontent.com/mickochow/channel/main/live$input.m3u8"

    # Check if the URL exists
    http_status=$(curl -o /dev/null -s -w "%{http_code}\n" "$streamingURL")

    if [[ "$http_status" -eq 404 ]]; then
#        echo "URL $streamingURL not found (404). Skipping to next input..."
        echo "live$input.m3u8 is not ready!"
        continue
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
