# vagrant_kubespray
kubespray로 쿠버네티스 설치

# 설치방법
1. vm 생성
```
vagrant up
```

2. ansible vm 원격접속
```
vagrant ssh kubernetes-master
```

3. clone kubespray project
```
git clone https://github.com/kubernetes-sigs/kubespray.git
```

4. 파이썬 패키지 설치
```
cd kubespray
sudo pip3 install -r requirements.txt
```

5. 인벤토리 복사
```
cp -rfp inventory/sample inventory/mycluster
```

6. IP 설정
```
declare -a IPS=(10.10.1.3 10.10.1.4 10.10.1.5)
CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```

7. 설치
```
ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root cluster.yml
```


# 참고자료
* https://jhooq.com/kubespray-12-steps-for-installing-a-production-ready-kubernetes-cluster/