
# Itinerary Highlights

### Create migration for Highlights table.
```ruby
class CreateHighlights < ActiveRecord::Migration[6.1]
  def change
    create_table :highlights do |t|
      t.integer :entity_id
      t.string  :entity_type
      t.text    :description
      t.references :Itinerary, null: false, foreign_key: true
      t.timestamps
    end

    add_index :highlights, [:entity_id, :entity_type]
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

## Controllers
``` ruby
class HighlightsController < ApplicationController
  def create
    @highlight = Highlight.create(highlight_params)
    redirect_to itinerary_path(@highlight.itinerary)
  end
  
  private
  def highlight_params
    params.require(:highlight).permit(:description, :entity_type, :entity_id, :itinerary_id)
  end
end
```

``` ruby
class ItinerariesController < ApplicationController
  
  def show
    @itinerary = Itinerary.find(params[:id])
    @highlights = @itinerary.hightlights.last(3)
  end
  
end
```

## View
``` ruby
<div class = "row">
  <div class = "highlights">
    <% @hightlights.each do |highlight| %>
        <div class="highlight">
            <%= image_tag highlight.entity.photo.url %>
            <%= highlight.description %>
        </div>
    <%= end %>
  </div>
</div>
```
