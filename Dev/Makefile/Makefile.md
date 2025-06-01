```make
.DEFAULT_GOAL=help

GROUPS=all

LOCAL_DOMAIN ?= api-test
EXEC_PHP = symfony php

  

$(eval LAST_COMMIT = $(shell git log -1 --oneline --pretty=format:"%h - %an, %ar"))

$(eval LAST_RELEASE = $(shell git describe --abbrev=0 --tags 2>/dev/null || echo "No tags yet")

  

help:

@echo ""

@echo " \033[1;34mAPI TEST\033[0m"

@echo " \033[1;34m------------\033[0m"

@echo ""

@grep -E '^[-a-z\:\\]+:.*?## .*$$|^##' $(MAKEFILE_LIST) | \

awk 'BEGIN {FS = ": .*?## "}; {gsub(/\\/, "", $$1); printf "\033[32m%-30s\033[0m %s\n", $$1, $$2}' | \

sed -e 's/\[32m##/[33m/'

@echo ""

@echo "Last release: \033[32m$(LAST_RELEASE)\033[0m"

@echo "Last commit : \033[32m$(LAST_COMMIT)\033[0m"

@echo ""

  

##

## Setup

##---------------------------------------------------------------------------

##

  

setup: setup\:vendor setup\:migrations ## Setup the project

  

setup\:vendor: ## Install dependencies locally

symfony composer install

  

setup\:migrations: ## Run database migrations

symfony console doctrine:migrations:migrate --no-interaction

  
  

##

## Server

##---------------------------------------------------------------------------

##

  

server\:up: ## Start the Symfony project

symfony proxy:domain:attach $(LOCAL_DOMAIN)

symfony proxy:start

symfony server:ca:install

symfony serve -d

  

server\:down: ## Stop the Symfony project

symfony server:stop

symfony proxy:stop

  

##

## Quality

##---------------------------------------------------------------------------

##

  

quality: quality\:phpstan quality\:cs-check quality\:grumphp ## Run all quality checks

  

quality\:phpstan: ## Run PHPStan analysis

$(EXEC_PHP) ./vendor/bin/phpstan analyse src --level=8

  

quality\:cs-check: ## Check code style

$(EXEC_PHP) ./vendor/bin/php-cs-fixer --allow-risky=yes --config=.php-cs-fixer.dist.php --using-cache=no --path-mode=intersection --verbose fix --dry-run --diff src tests

  

quality\:cs-fix: ## Fix code style

$(EXEC_PHP) ./vendor/bin/php-cs-fixer --allow-risky=yes --config=.php-cs-fixer.dist.php --using-cache=no --path-mode=intersection --verbose fix src tests

  

quality\:grumphp: ## Run GrumPHP checks

$(EXEC_PHP) ./vendor/bin/grumphp run

  

##

## Tests

##---------------------------------------------------------------------------

##

  

.PHONY: test-all test-unit test-functional coverage

  

test: test\:unit test\:functional ## Run all tests

  

test\:unit: ## Run unit tests

$(EXEC_PHP) ./vendor/bin/phpunit --testsuite=unit --testdox --colors=always

  

test\:functional: ## Run functional tests

$(EXEC_PHP) ./vendor/bin/phpunit --testsuite=functional --testdox --colors=always

  

coverage: ## Generate code coverage report

$(EXEC_PHP) -dxdebug.mode=coverage ./vendor/bin/phpunit --testsuite=unit --coverage-html coverage

  

##

## Messenger

##---------------------------------------------------------------------------

##

  

messenger\:consume: ## Consume messages from the queue

symfony console messenger:consume bi_messenger_queue --time-limit=3600

  

messenger\:failed-list: ## List failed messages

symfony console messenger:failed:show

  

messenger\:failed-retry: ## Retry failed messages

symfony console messenger:failed:retry
```