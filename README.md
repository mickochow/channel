<h1 align="center"> mickochow channel </h1>

Please enjoy the show by the following URL:

`https://raw.githubusercontent.com/mickochow/channel/main/liveXX.m3u8`

=======================

01: live01.m3u8

02: live02.m3u8

03: live03.m3u8

04: live04.m3u8

05: live05.m3u8

06: live06.m3u8

07: live07.m3u8

08: live08.m3u8

09: live09.m3u8

10: live10.m3u8

11: live11.m3u8

12: live12.m3u8

13: live13.m3u8

14: live14.m3u8

15: live15.m3u8

16: live16.m3u8

17: live17.m3u8

18: live18.m3u8

19: live19.m3u8

20: live20.m3u8

21: live21.m3u8

22: live22.m3u8

23: live23.m3u8

24: live24.m3u8

25: live25.m3u8

26: live26.m3u8

27: live27.m3u8

28: live28.m3u8

29: live29.m3u8

30: live30.m3u8

31: live31.m3u8

32: live32.m3u8

33: live33.m3u8

34: live34.m3u8

35: live35.m3u8

36: live36.m3u8

37: live37.m3u8

38: live38.m3u8

39: live39.m3u8

40: live40.m3u8

41: live41.m3u8

42: live42.m3u8

43: live43.m3u8

44: live44.m3u8

45: live45.m3u8

46: live46.m3u8

47: live47.m3u8

48: live48.m3u8

49: live49.m3u8

50: live50.m3u8

51: live51.m3u8

52: live52.m3u8

53: live53.m3u8

54: live54.m3u8

55: live55.m3u8

56: live56.m3u8

57: live57.m3u8

58: live58.m3u8

59: live59.m3u8

60: live60.m3u8

61: live61.m3u8

62: live62.m3u8

63: live63.m3u8

64: live64.m3u8

65: live65.m3u8

66: live66.m3u8

67: live67.m3u8

68: live68.m3u8

69: live69.m3u8

70: live70.m3u8

71: live71.m3u8

72: live72.m3u8

73: live73.m3u8

74: live74.m3u8

75: live75.m3u8

76: live76.m3u8

77: live77.m3u8

78: live78.m3u8

79: live79.m3u8

80: live80.m3u8

81: live81.m3u8

82: live82.m3u8

83: live83.m3u8

84: live84.m3u8

85: live85.m3u8

86: live86.m3u8

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
