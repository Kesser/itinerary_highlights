
# Itinerary Highlights

### Create migration for Highlights table.
```ruby
class CreateHighlights < ActiveRecord::Migration[6.1]
  def change
    create_table :highlights do |t|
      t.integer :entity_id
      t.string  :entity_type
      t.text    :description
      t.timestamps
    end

    add_index :highlights, [:entity_id, :entity_type]
    add_index :highlights, :itinerary_id

  end
end
```


### Highlight model with polymorphic relationships with Activity/Hotel/Location.
### Each highlight belongs to an itinerary.
``` ruby
class Highlight < ActiveRecord::Base
  belongs_to :entity, polymorphic: true
  belongs_to :itinerary
end
```


``` ruby
class Photo < ActiveRecord::Base
  attribute :url, :string
  ...
end
```
### Each model can have highlights.
``` ruby
class Location < ActiveRecord::Base
  attribute :name, :string
  has_one :photo
  has_many :highlights, as: :entity, dependent: :destroy
 
end

class Activity < ActiveRecord::Base
  attribute :name, :string
  has_one :photo
  has_many :highlights, as: :entity, dependent: :destroy
end

class Hotel < ActiveRecord::Base
  attribute :name, :string
  has_one :photo
  has_many :highlights, as: :entity, dependent: :destroy
end
```

### Each itinerary has many highlights.
```ruby
class Itinerary < ActiveRecord::Base
  has_many :highlights, dependent: :destroy
end
```



