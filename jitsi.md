# Jitsi

<https://blog.gtsop.me/jitsi-meet-performance-tuning.html>

## Solution to the Client computer bottleneck

[high cpu utilization on client end](https://community.jitsi.org/t/high-cpu-utilization-on-client-end/25764/12)

Jitsi meet work out of the box with up to about 15 participants. 
After that you will start to run into bottlenecks for users with slow computers that the user-interface redraw itself too much and consume 100% cpu.
This can be solved by profiling using the chrome performance monitor and disable all things that consume CPU time.

For example audio level monitoring that render the blue dots when someone talk trigger GUI updates. 
With many users these GUI updates will cause the client web-browser to redraw too frequently that will cause the user to have a bad audio experience due to missed audio updates. 
The solution is to simply disable the AudioLevels monitoring. disableAudioLevels: true,

On the left only 10% CPU when audio levels are disabled. 
On the right 100% CPU time when audio levels are enabled due to frequent audioLevelReport processing and GUI re-layout.
