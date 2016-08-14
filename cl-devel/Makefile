tagname=some-common-lisp
name=cl-devel
#run_options=--link some-mysql:mysql
run_options=

help:
	@echo "[WATCH OUT] this script will copy ~/.emacs.d and ~/.ssh/ to container ."
	@echo ""
	@echo "my .emac.d/init.el contains"
	@echo ""
	@echo '  (setq inferior-lisp-program "ros -Q run")'
	@echo '  (load "~/.roswell/helper.el")'
	@echo "  (slime-setup '(slime-fancy slime-indentation))"
	@echo ""
	@echo "if prepared type"
	@echo ""
	@echo "  make all"
clean-tmp:
	rm -rf .ssh
	cp -r ~/.ssh .
	rm -rf .emacs.d
copy-tmp:
	cp -r ~/.emacs.d .
	cp ~/.gitconfig .

all: clean-tmp copy-tmp
	docker build -f Dockerfile -t $(tagname) .
	make clean-tmp

no-cache: clean-tmp copy-tmp
	docker build --no-cache -f Dockerfile -t $(tagname) .
	make clean-tmp

run:
	mkdir work||true
	docker run -v /var/run/docker.sock:/var/run/docker.sock $(run_options) -v $(PWD)/work:/home/dev/work -d -P --name $(name) $(tagname)
ssh:
	ssh -l dev -i ~/.ssh/id_rsa -o "UserKnownHostsFile /dev/null" -o StrictHostKeyChecking=no 0.0.0.0 -p `docker port $(name)|awk 'NR==1'|awk -F: '{print $$2}'` 
kill:
	docker kill `docker ps -q -f name=$(name)`||true
	docker rm $(name)||true
restart:
	make kill||true
	make run

.PHONY: help all no-cache run ssh kill restart clean-tmp copy-tmp
