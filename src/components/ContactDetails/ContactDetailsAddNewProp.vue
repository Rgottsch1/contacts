<!--
  - @copyright Copyright (c) 2018 John Molakvoæ <skjnldsv@protonmail.com>
  -
  - @author John Molakvoæ <skjnldsv@protonmail.com>
  -
  - @license GNU AGPL version 3 or any later version
  -
  - This program is free software: you can redistribute it and/or modify
  - it under the terms of the GNU Affero General Public License as
  - published by the Free Software Foundation, either version 3 of the
  - License, or (at your option) any later version.
  -
  - This program is distributed in the hope that it will be useful,
  - but WITHOUT ANY WARRANTY; without even the implied warranty of
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
  - GNU Affero General Public License for more details.
  -
  - You should have received a copy of the GNU Affero General Public License
  - along with this program. If not, see <http://www.gnu.org/licenses/>.
  -
  -->

<template>
	<div class="grid-span-3 property property--last">
		<!-- title -->
		<property-title :icon="'icon-add'" :readable-name="t('contacts', 'Add new property')" />

		<div class="property__row">
			<div class="property__label" />

			<!-- type selector -->
			<multiselect :options="availableProperties" :placeholder="t('contacts', 'Choose property type')" class="property__value"
				track-by="id" label="name" @input="addProp" />
		</div>
	</div>
</template>

<script>
import rfcProps from 'Models/rfcProps'
import Contact from 'Models/contact'
import PropertyTitle from 'Components/Properties/PropertyTitle'

export default {
	name: 'ContactDetailsAddNewProp',

	components: {
		PropertyTitle
	},

	props: {
		contact: {
			type: Contact,
			default: null
		}
	},

	computed: {
		/**
		 * List of properties that the contact already have
		 *
		 * @returns {string[]}
		 */
		usedProperties() {
			return this.contact.jCal[1].map(prop => prop[0])
		},

		/**
		 * List of every properties you are allowed to add
		 * on this contact
		 *
		 * @returns {Object[]}
		 */
		availableProperties() {
			return Object.keys(rfcProps.properties)
				// only allow to add multiple properties OR props that are not yet in the contact
				.filter(key => rfcProps.properties[key].multiple || this.usedProperties.indexOf(key) === -1)
				// usable array of objects
				.map(key => {
					return {
						id: key,
						name: rfcProps.properties[key].readableName
					}
				}).sort((a, b) => a.name.localeCompare(b.name))
		}
	},

	methods: {
		/**
		 * Add a new prop to the contact
		 *
		 * @param {Object} data destructuring object
		 * @param {string} data.id the id of the property. e.g fn
		 */
		addProp({ id }) {
			let defaultData = rfcProps.properties[id].defaultValue
			let property = this.contact.vCard.addPropertyWithValue(id, defaultData ? defaultData.value : '')
			if (defaultData && defaultData.type) {
				property.setParameter('type', defaultData.type)
			}
		}
	}
}
</script>
