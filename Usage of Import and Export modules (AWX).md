#Usage of Import and Export modules (AWX)

Installing awx.awx collection
```
ansible-galaxy collection install awx.awx:21.5.0 --force
```

Installing awxkit using pip3
```
pip3 install --upgrade pip
```
```
pip3 install awxkit
```
Check version of awx
```
ansible-galaxy collection list
```

> References: 
> 
> https://docs.ansible.com/ansible/latest/collections/awx/awx/export_module.html#ansible-collections-awx-awx-export-module
> 
> https://docs.ansible.com/ansible/latest/collections/awx/awx/import_module.html#ansible-collections-awx-awx-import-module