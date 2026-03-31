# detach the current client.
press ctrl + b, then press d
tmux new -s miner
tmux ls
tmux a -t miner
tmux kill-session -t miner
exit 或者按下 Ctrl + d 会直接杀掉当前会话
