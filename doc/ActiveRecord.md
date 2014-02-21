ActiveRecord связи 

2.1. belongs_to
`
#rails g scaffold customer name:string
#rails g scaffold order name:string customer:belongs_to
`
```ruby
class Order < ActiveRecord::Base
  belongs_to :customer
end

class Customer < ActiveRecord::Base
  has_many :orders, dependent: :destroy # удаляем связанные записи
end
```

2.2. has_one
Связь has_one также устанавливает соединение один-к-одному с другой моделью, но в несколько ином смысле (и с другими последствиями). Эта связь показывает, что каждый экземпляр модели содержит или обладает одним экземпляром другой модели. Например, если каждый поставщик имеет только один аккаунт, можете объявить модель supplier подобно этому:

`
#rails g scaffold supplier name:string
#rails g scaffold account name:string supplier:belongs_to
`
```ruby
class Account < ActiveRecord::Base
  belongs_to :supplier
end

class Supplier < ActiveRecord::Base
  has_one :account
end
```

2.3. has_many

Связь has_many указывает на соединение один-ко-многим с другой моделью. Эта связь часто бывает на "другой стороне" связи belongs_to. Эта связь указывает на то, что каждый экземпляр модели имеет ноль или более экземпляров другой модели. Например, в приложении, содержащем покупателей и заказы, модель customer может быть объявлена следующим образом:

смотри пункт 1.

2.4. has_many :through

Связь has_many :through часто используется для настройки соединения многие-ко-многим с другой моделью. Эта связь указывает, что объявляющая модель может соответствовать нулю или более экземплярам другой модели через третью модель. Например, рассмотрим поликлинику, где пациентам (patients) дают направления (appointments) к врачам (physicians). Соответствующие объявления связей будут выглядеть следующим образом:

`
#rails g scaffold physician name:string
#rails g scaffold patient name:string

#rails g scaffold appointment patient:belongs_to physician:belongs_to appointment_date:datetime
`
```ruby
class Physician < ActiveRecord::Base
  has_many :appointments
  has_many :patients, through: :appointments
end
 
class Patient < ActiveRecord::Base
  has_many :appointments
  has_many :physicians, through: :appointments
end

class Appointment < ActiveRecord::Base
  belongs_to :physician
  belongs_to :patient
end
```

2.5. has_one :through

Связь has_one :through настраивает соединение один-к-одному с другой моделью. Эта связь показывает, что объявляющая модель может быть связана с одним экземпляром другой модели через третью модель. Например, если каждый поставщик имеет один аккаунт, и каждый аккаунт связан с одной историей аккаунта, тогда модели могут выглядеть так:
`
#rails g scaffold account_history account:belongs_to credit_rating:integer
#rails g migration AddAccountNumberToAccounts account_number:string
`
```ruby
class Supplier < ActiveRecord::Base
  has_one :account
  has_one :account_history, through: :account
end
 
class Account < ActiveRecord::Base
  belongs_to :supplier
  has_one :account_history
end
 
class AccountHistory < ActiveRecord::Base
  belongs_to :account
end
```

2.6. Связь has_and_belongs_to_many 

Связь has_and_belongs_to_many создает прямое соединение многие-ко-многим с другой моделью, без промежуточной модели. Например, если ваше приложение включает узлы (assemblies) и детали (parts), где каждый узел имеет много деталей, и каждая деталь встречается во многих узлах, модели можно объявить таким образом:
```ruby
class Assembly < ActiveRecord::Base
  has_and_belongs_to_many :parts
end
 
class Part < ActiveRecord::Base
  has_and_belongs_to_many :assemblies
end
```
`
#rails g scaffold assemblie name:string
#rails g scaffold part part_number:string

#rails g migration CreateJoinTable AssembliePart assemblie part
#rails g migration assemblies_parts assemblie:belongs_to part:belongs_to
`
```ruby
class CreateAssembliesAndParts < ActiveRecord::Migration
  def change
    create_table :assemblies do |t|
      t.string :name
      t.timestamps
    end
 
    create_table :parts do |t|
      t.string :part_number
      t.timestamps
    end
 
    create_table :assemblies_parts do |t|
      t.belongs_to :assembly
      t.belongs_to :part
    end
  end
end
```

Ссылки:
- http://www.scholarsandarticles.com/has_and_belongs_to_many/

