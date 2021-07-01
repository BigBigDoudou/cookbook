# frozen_string_literal: true

desc "Update README file"
task :readme do # rubocop:disable Metrics/BlockLength
  title_regex   = /\A#\s(?<t>.*)$/
  months        = %w[janvier février mars avril mai juin juillet août septembre octobre novembre décembre]
  types         = %w[Plat Sauce Entrée Accompagnement Pâte/pain Dessert Soupe].freeze
  list_to_regex = ->(list) { /(?i:(#{list.join("|")}))/.freeze }
  months_regex  = list_to_regex[months]
  types_regex   = list_to_regex[types]
  season_regex  = /\|\sSaison\s\|\s(?<s>#{months_regex})\sà\s(?<e>#{months_regex})\s\|/.freeze
  types_regex   = /\|\sType\s\|\s(?<t>#{types_regex})\s\|/.freeze

  File.open("README.md", "w") do |readme|
    readme.write <<~T
      # Livre de recettes

      | **RECETTES** | **TYPE** | #{months.map(&:chr).map(&:upcase).join(" | ")} |
      |#{Array.new(14) { ":---|" }.join}
    T

    Dir.glob("recettes/*.md").each do |file|
      content = File.read(file)

      title = content.match(title_regex)&.[]("t") || "Sans titre"
      type  = content.match(types_regex)&.[]("t") || "Autre"

      line = +"| [#{title}](./#{file}) | #{type} |"

      season_match = content.match(season_regex)
      s = season_match && months.find_index(season_match["s"]&.downcase) || 0
      e = season_match && months.find_index(season_match["e"]&.downcase) || 11

      12.times do |month|
        line << " "
        line << "✓" if e > s ? month.between?(s, e) : !month.between?(e, s)
        line << " |"
      end

      line << "\n"

      readme.write line
    end
  end
end

desc "Create a new recipe file"
task :new do
  require "active_support/core_ext/string/inflections"

  ARGV.each { |a| task(a.to_sym) }

  title = ARGV[1]

  file_name = ActiveSupport::Inflector.parameterize(title, separator: "_")
  file_name = file_name.tr("-", "_").gsub(/(?<![a-z])\w{1,2}(?![a-z])/, "_").gsub(/_{2,}/, "_")

  file_path = "wip/#{file_name}.md"

  File.open(file_path, "w") do |file|
    file.write "# #{title}\n"
    File.readlines("template.md").drop(1).each do |line|
      file.write line
    end
  end

  puts file_path
end
