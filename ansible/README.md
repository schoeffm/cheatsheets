
# Ansbile

## Ansible Modules

## Ansible Vault

Mit Ansible Vault kann man Variablen-Dateien (AES-)verschlüsseln. Bei 
Ausführung kann das Passwort interaktiv abgefragt od. aus einer Passwort-
Datei eingelesen werden.

    ansible-vault create foo.yml                                        # Erstellt verschl. Datei
    ansible-vault edit foo.yml                                          # Editieren einer Datei
    ansible-vault rekey foo.yml bar.yml baz.yml                         # Passwort ändern
    ansible-vault encrypt foo.yml bar.yml baz.yml                       # Verschl. von bestehenden Dateien
    ansible-vault decrypt foo.yml bar.yml baz.yml                       # Entschlüsseln
    ansible-playbook site.yml --ask-vault-pass                          # Ausführung mit Passwort-Abfrage
    ansible-playbook site.yml --vault-password-file ~/.vault_pass.txt   # Ausführung mit Passwort-Datei

für's Beispiel habe ich das alte Amazon-Passwort verwendet.

Bei der Arbeit mit [Vagrant](http://vagrantup.com) muss man allerdings 
etwas aufpassen (da man Optionen wie `--ask-vault-pass` nicht 
mitgeben kann). D.h. hier muss man die entspr. Vagrant-Configs nutzen:
	
	config.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook.yml"
      # ansible.sudo = true
      ansible.host_key_checking = false
      # ansible.verbose =  'vvvv'										# loggt einen Haufen Zeug beim Provisionieren
      ansible.ask_vault_pass = true										# Frägt den Nutzer wg. dem Vault-Passwort
      ansible.extra_vars = { 
        ansible_ssh_user: 'vagrant', 
        ansible_connection: 'ssh',
        ansible_ssh_args: '-o ForwardAgent=yes'
      }
    end