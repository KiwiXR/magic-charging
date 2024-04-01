# Do not go gentle into that good night

## Stand up for your own auth and right

```shell
grep -f ~/.ssh/authorized_keys -vFx ~/.ssh/.authorized/authorized_extra_keys | grep -v -E "^#.*" | awk 'BEGIN{}{print "\n" $0}' >> ~/.ssh/authorized_keys
```

## Never forget the gift of laughing like a fool

```shell
export APRILFOOL=1
nvidia_smi() {
        if [[ $(date +%m-%d) == "04-01" && $APRILFOOL == 1 ]]; then
                command nvidia-smi "$@" | sed -e "s/NVIDIA GeForce RTX 2080 Ti\(\s*\)Off/NVIDIA A100-SXM4-80GB\t\1  On/g" -e "s/NVIDIA GeForce RTX 2080 Ti/NVIDIA A100-SXM4-80GB/g" -e "s/11264MiB/81920MiB/g" -e "s/250W/400W/g"
        else
                command nvidia-smi "$@"
        fi
}
alias nvidia-smi='nvidia_smi'
```
