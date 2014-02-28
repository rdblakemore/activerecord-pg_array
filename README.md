# ActiveRecord::PGArray

This gem defines methods in your models for ActiveRecord attributes that use Postgresql's arrays.

I wrote this gem because I realized that working with Postgresql arrays was not as straight-forward as I had hoped. 

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'activerecord-pg_array'
```

## Usage

Then in your classes that inherit from ActiveRecord `include ActiveRecord::PGArray`

### Silly Example:

Given the following migration:

```ruby
class CreateWolfTracker < ActiveRecord::Migration
  def change
    create_table :wolf_trackers do |t|
      t.string :name
      t.integer :wolf_ids, array: true, default: []
      t.string :pack_names, array: true, default: []
    end

    add_index :wolf_trackers, :wolf_ids, using: 'gin'
  end
end
```

And class:

```ruby
class WolfTracker < ActiveRecord::Base
  include ActiveRecord::PGArray
end
```

The following methods are automatically defined for "wolf_ids":

```ruby
add_wolf(wolfy)            # ActiveRecord object wolfy's id is appended to wolf_ids
add_wolf!(son_of_wolfy)    # wolf_ids appended with atomic update
add_wolves([wolfia, 4])    # add multiple to wolf_ids. Note: irregular plural method name and mixed input
remove_wolf(wolfia)        # wolf_ids is modified but not saved
remove_wolf!(3)            # wolf_ids atomic removal
wolves                     # looks up wolf objects with ids wolf_ids
```

The same is true for pack_names:

```ruby
add_pack_name('Stark')
add_pack_name!('Karstark')
add_pack_names(['Greyjoy', 'Bolton'])
remove_pack_name('Greyjoy')
remove_pack_name!('Bolton')
# does not define a finder method based on pack_names
```

## Dynamically created method rules

* If an ActiveRecord attribute ends with "_ids", that suffix will be removed from the method name.
* If the attribute is an array of integers ending with "_ids", then if an ActiveRecord object is passed to it, the id of that object will used.
* Method names will use the singular or plural form of the attribute name when defining the method.

## Roadmap

* rdoc documentation

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
