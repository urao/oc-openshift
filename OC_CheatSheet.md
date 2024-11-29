## OC commands


```
for node in $(oc get nodes -o jsonpath='{.items[*].metadata.name}'); \
  do oc debug node/${node} -- chroot /host shutdown -h 1; done 

oc debug node/server2-w1 -- chroot /host shutdown -h 1
oc debug node/server2-w2 -- chroot /host shutdown -h 1
oc debug node/server2-w3 -- chroot /host shutdown -h 1
```
