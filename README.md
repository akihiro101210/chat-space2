
# README

## データベース設計

##### messagesテーブル
>チャットのメッセージを保存する

columns |type             |constraint       |index
:-------|:----------------|:----------------|:----:
body    |text             |                 |-
image   |string           |                 |-
group_id|references :group|foreign_key: true|○※
user_id |references :user |foreign_key: true|○※1

※ グループごとにメッセージが管理されているため、検索しやすいようにgroup_idにindexを貼る

<br>

##### usersテーブル
>ユーザーを保存する

columns           |type  |constraint                   |index
:-----------------|:-----|:----------------------------|:----:
name              |string|null: false, unique: true    |○※

※ グループにチャットメンバーを追加するときに名前から選択するため、unique: trueとしてindexを貼る

<br>

##### groupsテーブル
>グループを保存する

columns|type  |constraint   |index
:------|:-----|:------------|:----:
name   |string|null: false  |-

<br>

##### group_membersテーブル
>グループとユーザーの対応関係を保存する

columns |type             |constraint       |index
:-------|:----------------|:----------------|:----:
group_id|references :group|foreign_key: true|○※
user_id |references :user |foreign_key: true|○※

※ groups <=> users間の参照を高速化するため、indexを貼る

<br>

### アソシエーション
    class Message < ActiveRecord::Base
      belongs_to :user
      belongs_to :group
    end

    class User < ActiveRecord::Base
      has_many :messages
      has_many :group_members
      has_many :groups, through: :group_members
    end

    class Group < ActiveRecord::Base
      has_many :messages
      has_many :group_members
      has_many :users, through: :group_members
    end

    class GroupMember < ActiveRecord::Base
      belongs_to :user
      belongs_to :group
