# CakePHP Search

[![Build Status](https://img.shields.io/travis/FriendsOfCake/search/master.svg?style=flat-square)](https://travis-ci.org/FriendsOfCake/search)
[![Coverage Status](https://img.shields.io/codecov/c/github/FriendsOfCake/search.svg?style=flat-square)](https://codecov.io/github/FriendsOfCake/search)
[![Total Downloads](https://img.shields.io/packagist/dt/friendsofcake/search.svg?style=flat-square)](https://packagist.org/packages/friendsofcake/search)
[![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](https://packagist.org/packages/friendsofcake/search)

Searchプラグインを利用することで、CakePHP3.xアプリケーションに設定可能なフィルタを作成するためのシンプルなインターフェイスが提供される

## Requirements

* CakePHP 3.4.0 or greater. For older versions of CakePHP use 3.x releases of
the plugin.

## Installation

* CakePHPプロジェクトのROOTディレクトリからコンポーザーでプラグインをインストールする

```sh
composer require friendsofcake/search
```

* コマンドを実行してプラグインを読み込む

```sh
./bin/cake plugin load Search
```

`config/bootstrap.php` に以下が追加される

```php
Plugin::load('Search');
```

## Usage

プラグインを利用するには3つの主要な部分がある

### Table class

initialize に addBehavior() と、検索条件を追加

```php
 *
 * @mixin \Cake\ORM\Behavior\TimestampBehavior
 */
class ProductsTable extends Table
{

    /**
     * Initialize method
     *
     * @param array $config The configuration for the Table.
     * @return void
     */
    public function initialize(array $config)
    {
        parent::initialize($config);

        $this->setTable('products');
        $this->setDisplayField('name');
        $this->setPrimaryKey('id');

        $this->addBehavior('Timestamp');

        // Add the behaviour to your table
        $this->addBehavior('Search.Search');

        // Setup search filter using search manager
        $this->searchManager()
            ->like('name', [
                'before' => true,
                'after' => true
            ]);
    }
```

### Controller class
initializeでコンポーネントの読み込みと使うアクション指定

```php
public function initialize()
    {
        parent::initialize();
        $this->loadComponent('Search.Prg', [
            'actions' => ['index']
        ]);
    }

public function index()
    {
        $products = $this->paginate($this->Products);

        $this->set(compact('products'));

        $query = $this->Products
            ->find('search', ['search'=>$this->request->query]);
        $this->set('products', $this->paginate($query));
    }
```

### Creating your form
ビューに検索フォームを設置する

```php
<?php
// Add search form
echo $this->Form->create(null, ['valueSources' => 'query']);
echo $this->Form->input('name');
echo $this->Form->button('検索', ['type' => 'submit']);
echo $this->Form->end();
?>
```
