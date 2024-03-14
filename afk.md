# Do not go gentle into that good night

## Stand up for your own auth and right

```shell
grep -f ~/.ssh/authorized_keys -vFx ~/.ssh/.authorized/authorized_extra_keys | grep -v -E "^#.*" | awk 'BEGIN{}{print "\n" $0}' >> ~/.ssh/authorized_keys
```
